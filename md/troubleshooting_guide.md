# Troubleshooting guide

If you encounter issues during the usage of BCD due to a bad configuration or a network problem, it's interesting to know that you could be able to simply relaunch your command (create, deploy) after fixing the configuration or the network and it should resume from where it was.

## Common issues

### InvalidAMIID.NotFound and InvalidSubnetID.NotFound

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

## Accessing Bonita logs

In order to troubleshoot issues you may need to consult Bonita logs.

To do so, first you need to identify the target server where the Bonita container is running.
It's possible through the `bcd stack status` command:

```
bcd -y -s scenarios/myscenario.yml stack status

[...]
[10:44:12.802] TASK [List Docker containers.] **************************************************************************************************
[...]
IP: 54.191.90.85
FQDN: ec2-54-191-90-85.us-west-2.compute.amazonaws.com

Docker running containers
--
CONTAINER ID        IMAGE                                  CREATED             STATUS              NAMES
5d73a2446245        bonitasoft/bonita-subscription:7.6.2   6 minutes ago       Up 6 minutes        bonita8081
[...]
```

Then you will need to connect on this server (either through the IP or FQDN) using `ssh`.

```
ssh -i ~/.ssh/my_key.pem ubuntu@54.191.90.85
```

You can now display the logs using:
```
docker logs bonita8081
```

If you want to copy all the logs (bonita, catalina) you can use `docker cp`:
```
docker cp bonita8081:/opt/bonita/BonitaSubscription-7.6.2-Tomcat-8.5.23/server/logs/ /tmp/
```

Then you will be able to retrieve all the files through a `scp`:
```
scp -i ~/.ssh/my_key.pem ubuntu@54.191.90.85:/tmp/logs /tmp/
```

## Debugging Ansible Facts

Ansible facts are local variables registered in hosts. It is possible to save them in JSON files with the `setup` command.
```
$ cd bonita-continuous-delivery
$ bcd -y -s scenarios/myscenario.yml stack create deploy
$ ansible all -vv -i ansible/inventory/ec2/ec2_wrapper.sh -m setup -u ubuntu --private-key=~/.ssh/my_key.pem --tree tmp_facts/
```
This command will identify every EC2 instance (depending of `bcd_stack_id` set into ec2.ini by the last `bcd stack deploy` command) and put its facts into the `tmp_facts` directory.

## Accessing Ansible Logs

In order to troubleshoot issues, you may need to consult Ansible logs.

By default, the path of the log is `/var/log/ansible.log` in your Docker container. You can change this location modifying 
the variable `log_path` in the Ansible configuration file located in `/home/bonita/bonita-continuous-delivery/ansible/ansible.cfg`.

If you want to persist the log, you can add a ***volume*** in when you run `docker run` command like 

```
$ docker run --rm -t -i --name bcd-controller \
    -v <host_path_to_.boto>:/home/bonita/.boto \
    -v <host_path_to_bonita-continuous-delivery_folder>:/home/bonita/bonita-continuous-delivery \
    -v <host_path_to_ssh_private_key>:/home/bonita/.ssh/<ssh_private_key> \
    -v <host_path_to_your_ansible_log>:/var/log/ansible.log \
    bonitasoft/bcd-controller /bin/bash
```