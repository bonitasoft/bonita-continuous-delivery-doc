# How to run BCD with an UID different to 1000(Linux Environment)
The BCD controller run with the user `bonita` with the UID and GID 1000 which it is the first numerical user and group 
by default in Linux, subsequent entries for users are greater than 1000.

In some cases, your runtime host user could have a UID different to 1000 and when you try to access to your mounted 
volume in the BCD container you will have a `Permission denied`.

To solve this problem you can run your BCD controller adding the option `-u UID:GID` to the `docker run` command to 
change the `bonita` user UID ONLY in your container.

**Example with a UID 1001 and GID 1001**:

```
$ docker run --rm -t -i --name bcd-controller -u 1001:1001 \
    -v <local_path_to_.boto>:/home/bonita/.boto \
    -v <local_path_to_bonita-continuous-delivery_folder>:/home/bonita/bonita-continuous-delivery \
    -v <local_path_to_ssh_private_key>:/home/bonita/.ssh/<ssh_private_key> \
    bonitasoft/bcd-controller /bin/bash

The new UID for bonita user is 1001
bonita@control_machine:~$   
    
```
