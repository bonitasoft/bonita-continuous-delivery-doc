# Debugging Facts

Ansible facts are local variables registered in hosts. It is possible to save them in JSON files with the `setup` command.
```
$ ansible all -vv -i inventory/ec2/ec2_wrapper.sh -m setup -u ubuntu --tree tmp_facts/
```
This command will identify every EC2 instance (regardless of `bcd_stack_id`) and put its facts into the `tmp_facts` directory.
