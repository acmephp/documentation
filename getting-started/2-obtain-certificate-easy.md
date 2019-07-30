---
currentMenu: getting-started-obtain-certificate
---

# Obtain a certificate

Now Acme PHP is available on your system (`php acmephp.phar --version` should display its version),
you can start requesting certificates for your domains using it.

**Note:** This is the recommended way to request a certificate, but you can
achieve the same purpose by following the long way and [running several
commands one by one](/documentation/getting-started/2-obtain-certificate-hard.html)

## 1. Create a configuration file

``` yaml
contact_email: contact@company

defaults:
  distinguished_name:
      country: FR
      locality: Paris
      organization_name: MyCompany
  solver: http

certificates:
  - domain: example.com
    distinguished_name:
      organization_name: MyCompany Internal
    solver: route53
    subject_alternative_names:
      - '*.example.com'
      - www.subdomain.example.com
    install:
      - action: install_aws_elb
        region: eu-west-1
        loadbalancer: my_elb
        listener: 443
  - domain: www.example.com
    solver:
      name: http-file
      adapter: ftp       # ftp or sftp or local, see https://flysystem.thephpleague.com/
      root: /var/www/
      host: ftp.example.com
      username: username
      password: password
      # port: 21
      # passive: true
      # ssl: true
      # timeout: 30
      # privateKey: path/to/or/contents/of/privatekey
```

## 2. Get your certificate

``` console
php acmephp.phar run config.yml
```

This command will do many things:
- register your account key in the Let's Encrypt/ACME server, associating it with your e-mail address
- for each certificate configured in the file
- ask the ACME server for a token and ask to the configured solver to expose the token
- locally check that the token is well exposed
- ask the ACME server to validate the domain
- ask the ACME server to generate a certificate
- install the certificate by using the configured action

Next: [Configure your webserver](/documentation/getting-started/3-configure-webserver.html)
