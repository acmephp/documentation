---
currentMenu: guides-multidomain-certificate
---

# Multi-domains certificates

Let's Encrypt and most ACME servers are able to provide multi-domain certificates. Such certificates
will be usable for multiple domains as a single file, which can be useful in many cases (for instance to
use the same certificate for `yourdomain.org` and `www.yourdomain.org`).

## Obtain a multi-domain certificate

The process of obtaining a multi-domain certificate is almost the same as
[getting one for a single domain](/documentation/getting-started/2-obtain-certificate.html).

The only difference is when you use the `request` command, to add the other domains you want to support:

``` console
php acmephp.phar request yourdomain.org -a www.yourdomain.org -a anotherdomain.org
```

**Note**: you need to be the authorized owner of all the domains you request this way. In this case, it means
you have to run the `authorize` and `check` commands for the domains `yourdomain.org`, `www.yourdomain.org`
and `anotherdomain.org` before being able to request the multi-domains certificate.

The process will be the same as with a simple certificate: the command will ask you some informations, request
the certificate to the ACME server and store it under the `~/.acmephp` directory.

The first domain will be considered the main domain of the certificate and any additionnal domain (provided using `-a`)
will be considered an alternate domain. This means that Acme PHP will store all the certificate informations under the
main domain (even if the certificate is a multi-domain one). In our case, this means 6 files will be created in the
storage directory:
  
- **The full-chain certificate** at `~/.acmephp/master/certs/yourdomain.org/fullchain.pem`
- **The chain alone** at `~/.acmephp/master/certs/yourdomain.org/chain.pem`
- **The certificate alone** at `~/.acmephp/master/certs/yourdomain.org/cert.pem`
- **The combined certificate** at `~/.acmephp/master/certs/yourdomain.org/combined.pem`
- **The certificate private key** at `~/.acmephp/master/private/yourdomain.org/private.pem`
- **The certificate public key** at `~/.acmephp/master/private/yourdomain.org/public.pem`

Check the [single domain documentation](/documentation/getting-started/2-obtain-certificate.html#4-get-your-certificate)
for more informations about these files.

**Note**: using the main domain as storage directory means that if you request another certificate with the same
main domain, the previous one will be erased.

Once requested, if you run the `status` command, you will see the following:

``` console
+-------------------------+----------------------------+---------------------+---------------------+----------------+
| Domain                  | Issuer                     | Valid from          | Valid to            | Needs renewal? |
+-------------------------+----------------------------+---------------------+---------------------+----------------+
| yourdomain.org          | Let's Encrypt Authority X3 | 2016-09-23 10:12:00 | 2016-12-22 10:12:00 | No             |
|  ├── www.yourdomain.org |                            |                     |                     |                |
|  └── anotherdomain.org  |                            |                     |                     |                |
+-------------------------+----------------------------+---------------------+---------------------+----------------+
```

## Renew a multi-domain certificate

The process is the same as for a single domain certificate: re-run the request command:

``` console
php acmephp.phar request yourdomain.org -a www.yourdomain.org -a anotherdomain.org
```
