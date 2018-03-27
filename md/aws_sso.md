# AWS Single Sign-On

AWS allows to integrate [numerous Third-Party SAML Solution Providers](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_saml_3rd-party.html).

If you implement this kind of authentication, when you start the BCD Controller container it will no longer be necessary to mount the `boto` file.

```
$ docker run --rm -t -i --name bcd-controller \
    -v <local_path_to_bonita-continuous-delivery_folder>:/home/bonita/bonita-continuous-delivery \
    -v <local_path_to_ssh_private_key>:/home/bonita/.ssh/<ssh_private_key> \
    bonitasoft/bcd-controller /bin/bash
```

Indeed you will use `~/.aws` directory instead.

## Single Sign-On to AWS Using G Suite

In this step we will provide you an example using G Suite from Google.

First, set up a Single Sign-On to AWS using G Suite as described in [AWS website](https://aws.amazon.com/es/blogs/security/how-to-set-up-federated-single-sign-on-to-aws-using-google-apps/).

The BCD Controller already embeds [aws-google-auth](https://github.com/cevoaustralia/aws-google-auth) to manage the authentication.

You will need to know Google's assigned Identity Provider ID (idp-id) and the ID assigned to the SAML service provider (sp-id).  
`idp-id` can be found on Google Admin `Security > Set up single sign-on (SSO)` page in the SSO url provided. For instance: `https://accounts.google.com/o/saml2/idp?idpid=123456789012`  
`sp-id` can be found into the URL of your browser when viewing Google Admin `Apps > SAML Apps > Amazon Web Services` page. For instance: `#AppDetails:service=123456789012`  

After launching the BCD Controller you will have to authenticate yourself as below:
```
aws-google-auth --idp-id Abc012345 --sp-id 123456789012 -p default -u john.doe@acme.com
```
`aws-google-auth` will store the credentials into `~/.aws` directory through the `default` AWS profile

If you want to use a different profile, you will have to export the variable accordingly:
```
aws-google-auth --idp-id Abc012345 --sp-id 123456789012 -p test -u john.doe@acme.com
export AWS_PROFILE=test
```
