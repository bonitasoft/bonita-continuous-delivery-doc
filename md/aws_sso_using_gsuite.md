# Single Sign-On to AWS Using G Suite

It's possible to set up a Single Sign-On to AWS using G Suite as described into the [AWS website](https://aws.amazon.com/es/blogs/security/how-to-set-up-federated-single-sign-on-to-aws-using-google-apps/)

If you want to use this it will allow you to mount the BCD Controller without a boto file.
Instead of that [aws-google-auth](https://github.com/cevoaustralia/aws-google-auth) will manage the authentication.

Then you will just have to modify the scenario in order to let BCD be aware of this authentication mode :

```
aws_sso : gsuite
```

## Start a container

You will need to know Google's assigned Identity Provider ID (idp-id) and the ID that they assign to the SAML service provider (sp-id).
The `idp-id` can be found into `Security > Set up single sign-on (SSO)` in the SSO url provided `https://accounts.google.com/o/saml2/idp?idpid=123456789012`
The `sp-id` can be found into `Apps > SAML Apps > Amazon Web Services` in the browser url you will see `#AppDetails:service=123456789012`

```
$ docker run --rm -t -i --name bcd-controller \
    -v <local_path_to_bonita-continuous-delivery_folder>:/home/bonita/bonita-continuous-delivery \
    -v <local_path_to_ssh_private_key>:/home/bonita/.ssh/<ssh_private_key> \
    bonitasoft/bcd-controller /bin/bash
$ cd /home/bonita/bonita-continuous-delivery
aws-google-auth --idp-id Abc012345 --sp-id 123456789012 -p default -u john.doe@acme.com
```
`aws-google-auth` will store the credentials into `~/.aws` directory through the `default` AWS profile
