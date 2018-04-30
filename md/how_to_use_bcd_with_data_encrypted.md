# How to use BCD with sensitive data encrypted

BCD involves providing sensitive data like passwords or private credentials through [YAML Scenario files](scenarios.md). Although you may restrict access to scenario files using file system permissions, it may be advisable to encrypt these sensitive data for even more security.  
Therefore BCD supports [Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html) to encrypt sensitive data. BCD automatically decrypts Vault-encrypted content at runtime when the decryption password is provided.

In this tutorial, we will explain how to encrypt files with `ansible-vault` and how to configure BCD to decrypt these Vault-encrypted data.


1. **Create a Vault password file**

    A password is required to encrypt/decrypt your sensitive data. BCD requires this password to be stored in a file which path is set in the **`ANSIBLE_VAULT_PASSWORD_FILE`** environment variable.

    ::: warning
    :fa-exclamation-triangle: **Password file permissions**  
    Ensure permissions on the file are such that no one else can access your password and do not add your password file to source control.
    :::

    The password should be a string stored as a single line in the file.
    
    **Example**:  
    In your BCD controller, create a `vault_pass` file with a **MyV@ultPa55** password and set the `ANSIBLE_VAULT_PASSWORD_FILE` accordingly:
    ```
    bonita@bcd-controller:~/bonita-continuous-delivery$ echo "MyV@ultPa55" > vault_pass
    bonita@bcd-controller:~/bonita-continuous-delivery$ chmod 600 vault_pass
    bonita@bcd-controller:~/bonita-continuous-delivery$ ls -l $(pwd)/vault_pass 
    -rw-------    1 bonita   bonita          12 Apr 30 09:40 /home/bonita/bonita-continuous-delivery/vault_pass
    bonita@bcd-controller:~/bonita-continuous-delivery$ export ANSIBLE_VAULT_PASSWORD_FILE=/home/bonita/bonita-continuous-delivery/vault_pass
    ```

2. **Encrypt your BCD scenario file**

    Assuming your BCD scenario file contains sensitive data, we are now going to encrypt this file using the `ansible-vault encrypt` command.

    **Example**:  
    In your BCD controller, encrypt your `scenarios/myscenario.yml` file using the Vault password defined in previous step:
    ```
    bonita@bcd-controller:~/bonita-continuous-delivery$ ansible-vault encrypt scenarios/myscenario.yml
    Encryption successful
    ```
    As a result, the `scenarios/myscenario.yml` file is Vault-encrypted. It may be safely added to source control as long as the Vault password remains confidential.

    ::: info
    :fa-info-circle: **View / Edit / Decrypt**  
    If you need to view or edit the scenario content, you may use the `ansible-vault view` and `ansible-vault edit` commands respectively.  
    You may also decrypt your scenario file using the `ansible-vault decrypt` command.  
    Refer to the [Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html) documentation for detailed information about this feature.
    :::

3. **Run BCD commands as usual**

    You may now run BCD commands without any change. BCD and Ansible will automatically decrypt your Vault-encrypted data.


Caveats <!--{.h2}-->

- You may encrypt several files but the password must be the same for all files you wish to use together at the same time.
- Although Ansible supports prompting for a password, BCD only supports a password to be provided through a password file defined by the `ANSIBLE_VAULT_PASSWORD_FILE` environment variable.
