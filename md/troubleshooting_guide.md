# Troubleshooting guide

If you encounter issues during the usage of BCD due to a bad configuration or a network problem, it's interesting to know that you could be able to simply relaunch your command (create, deploy) after fixing the configuration or the network and it should resume from where it was.

## InvalidAMIID.NotFound and InvalidSubnetID.NotFound

If while you launch a `create` command in order to create instances on AWS you get errors like these:

```
[...]
InvalidAMIID.NotFound: The image id '[ami-fc4f5e85]' does not exist
[...]
Error launching source instance: InvalidSubnetID.NotFound: The subnet ID 'subnet-ab123456' does not exist
[...]
```

You should check that `ec2_subnet_ids` and `aws_ami` correspond to the region set into `ec2_region`.
You may also check that rights linked to your AWS credentials (`.boto` or `.aws`) correspond to the actions in your scenario.

## Access to private maven repository is denied

In order to configure BCD to use a corporate maven repository, define it as a mirror for maven central
Add a regular maven `settings.xml` file with the proper [mirror configuration](https://maven.apache.org/guides/mini/guide-mirror-settings.html) in `/home/bonita/.m2` folder inside `bcd controller` container:

If you want to update an already running container without restart, you can run the following, otherwise use docker volume binding.

```bash
# .m2 folder may be created before
docker cp <PATH-TO-SETTINGS.xml> bcd-controller:/home/bonita/.m2/settings.xml
```

## Restricted Internet access by an enterprise proxy

If required, you can configure BCD to use proxy settings.

This has to be done for the docker container, the maven installation.
If you are using the bcd stack commands, you may need to configure ansible as well.

* Docker proxy settings : [docker network/proxy](https://docs.docker.com/network/proxy)
* Maven proxy settings: [maven proxy configuration](https://maven.apache.org/guides/mini/guide-proxies.html)
* Ansible proxy settings: [Ansible environment](https://docs.ansible.com/ansible/2.9/user_guide/playbooks_environment.html#setting-the-environment-and-working-with-proxies)

::: info
:fa-lightbulb-o: Starting from BCD 3.4, you can directly bind mount your host maven `~/.m2` folder. 
This will give BCD access to your already configured maven settings (enterprise repository configuration, proxy settings, etc.)
and you will also benefit from the already cached artifacts present in your `~/.m2/repository` folder.
:::

## Accessing Bonita logs

In order to troubleshoot issues you may need to consult Bonita logs.

To do so, first you need to identify the target server where the Bonita container is running.
It's possible through the `bcd stack status` command:

```bash
bcd -s scenarios/myscenario.yml -y stack status

[...]
[10:44:12.802] TASK [List Docker containers.] **************************************************************************************************
[...]
IP: 54.191.90.85
FQDN: ec2-54-191-90-85.us-west-2.compute.amazonaws.com

Docker running containers
--
CONTAINER ID        IMAGE                                  CREATED             STATUS              NAMES
5d73a2446245        bonitasoft/bonita-subscription:7.11.0   6 minutes ago       Up 6 minutes        bonita8081
[...]
```

Then you will need to connect on this server (either through the IP or FQDN) using `ssh`.

```bash
ssh -i ~/.ssh/my_key.pem ubuntu@54.191.90.85
```

You can now display the logs using:
```bash
docker logs bonita8081
```

If you want to copy all the logs (bonita, catalina) you can use `docker cp`:
```bash
docker cp bonita8081:/opt/bonita/BonitaSubscription-7.11.0-Tomcat-8.5.34/server/logs/ /tmp/
```

Then you will be able to retrieve all the files through a `scp`:
```bash
scp -i ~/.ssh/my_key.pem ubuntu@54.191.90.85:/tmp/logs /tmp/
```

## Debugging Ansible Facts

Ansible facts are local variables registered in hosts. It is possible to save them in JSON files with the `setup` command.
```bash
$ cd bonita-continuous-delivery
$ bcd -s scenarios/myscenario.yml -y stack create deploy
$ ansible all -vv -i ansible/inventory/ec2/ec2_wrapper.sh -m setup -u ubuntu --private-key=~/.ssh/my_key.pem --tree tmp_facts/
```
This command will identify every EC2 instance (depending of `bcd_stack_id` set into ec2.ini by the last `bcd stack deploy` command) and put its facts into the `tmp_facts` directory.

## Accessing Ansible Logs

In order to troubleshoot issues, you may need to consult Ansible logs.

By default, the path of the log is `/var/log/ansible.log` in your Docker container. You can change this location modifying the variable `log_path` in the Ansible configuration file located in `/home/bonita/bonita-continuous-delivery/ansible/ansible.cfg`.

If you want to persist the log, you can add a ***volume*** in when you run `docker run` command like

```bash
$ docker run --rm -t -i --name bcd-controller \
    -v <host_path_to_.boto>:/home/bonita/.boto \
    -v <host_path_to_bonita-continuous-delivery_folder>:/home/bonita/bonita-continuous-delivery \
    -v <host_path_to_ssh_private_key>:/home/bonita/.ssh/<ssh_private_key> \
    -v <host_path_to_your_ansible_log>:/var/log/ansible.log \
    quay.io/bonitasoft/bcd-controller /bin/bash
```

## Files owned by another user in the workspace (Linux users only)

BCD is packaged as a docker image, and the user inside the image is mapped to the most common default
`user id` and `group id` (for the first created user) on linux platform which is `1000`

Since BCD creates files during execution, if your `user id` doesn't match the `user id` inside the docker image, you will end
with new files under BCD workspace owned by another user than you and won't be able to edit or delete them
unless having admin privileges.

_Example: the `dependencies` folder:_
````bash
ls -la dependencies/
-rw-r--r-- 1 <my-user-id> <my-group-id>  157 déc.   4  2019 README.md
drwxr-xr-x 2 root   root                 4096 janv. 6  2020 7.11.3 # <= this folder created by BCD is now read only for <my-user>
````

To properly map your own user to the user inside the BCD controller image, 
see the `Running BCD controller with user ID different from 1000` paragraph in [BCD Controller image](bcd_controller.md)
