---
currentMenu: getting-started-obtain-certificate
---

# Obtain a certificate

Now Acme PHP is available on your system (`php acmephp.phar --version` should display its version),
you can start requesting certificates for your domains using it.

**Note:** This is the long way to abtain a certificate. The
[recommended way describe here](/documentation/getting-started/2-obtain-certificate-easy.html)

Several steps are required to do so (note that only the 4th one will be necessary during renewal):

1. [Register on the Let's Encrypt/ACME server](#1-register-on-the-lets-encryptacme-server)
2. [Prove you own the domain](#2-prove-you-own-the-domain)
3. [Ask the server to check your proof](#3-ask-the-server-to-check-your-proof)
4. [Get your certificate](#4-get-your-certificate)
5. [Renew your certificate](#5-renew-your-certificate)
6. [List the certificates handled by Acme PHP](#6-list-the-certificates-handled-by-acme-php)

## 1. Register on the Let's Encrypt/ACME server

The first thing we need to do is to generate a global private key (your "account key") that will be
used for future exchanges with Let's Encrypt to prove your identity.

To do so, run the following command:

``` console
php acmephp.phar register youremail@example.com
```

This command will do three main things:
- create a default configuration file `~/.acmephp/acmephp.conf` that you may edit for advanced usages of Acme PHP
- generate a key pair for your account and store it in `~/.acmephp/master/account`
- register your account key in the Let's Encrypt/ACME server, associating it with your e-mail address

## 2. Prove you own the domain

You can only request certificates for domains you own so you need to prove to the Let's Encrypt server that you own
the domain.

The principle is quite simple:
- You ask the server for a token and expose it on a specific URL given by the server
- The server check that URL and verify the token is properly exposed there

**Note:** This technique is called HTTP checking as it uses a HTTP request to check your ownership.
You can also use [DNS checking](/documentation/guides/dns-challenge.html) which uses a DNS TXT field to expose the token
(it may be easier in your specific context).

Using this technique, you proved your server is responding behind the concerned domain.

**Note:** You'll need to periodically prove that you own a domain (certificates renewals may require a new token).

The first step is to get and expose the token. Run the following:

``` console
# Get a token to expose
php acmephp.phar authorize yourdomain.org

# The command will display explainations to help you expose the token properly.
# Usually, a simple text file is enough to expose the token.
```

## 3. Ask the server to check your proof

Once the token exposed (don't hesitate to check in your browser), run the following command to ask the server
to check your proof:

``` console
php acmephp.phar check yourdomain.org

# The server will check the URL for the token to expose. If it succeeds, you will be able to request certificate
# for this domain.
```

If there is an issue, you will usually have a message describing the problem. If you are stuck, don't hesitate to
ask on [Github issues](https://github.com/acmephp/acmephp/issues).

## 4. Get your certificate

Finally! You are the proved owner of your domain, you can now request and renew a certificate for this domain.
To do so, simply run the `request` command:

``` console
php acmephp.phar request yourdomain.org
```

This command will ask you required informations for the certificate, request it to the server and store it in
the `~/.acmephp` storage directory.

6 files will be created in the storage directory:

- **The full-chain certificate** at `~/.acmephp/master/certs/yourdomain.org/fullchain.pem`.
  This file is the certificate itself. You probably want to use this file in your webserver configuration as it
  includes all the issuers chain for a better compatibility with old devices.

- **The certificate private key** at `~/.acmephp/master/private/yourdomain.org/private.pem`.
  This file is usually required by the webserver.

- **The chain alone** at `~/.acmephp/master/certs/yourdomain.org/chain.pem`.
  Some webservers requires a separated chain and certificate. This is the chain part, if you need it.

- **The certificate alone** at `~/.acmephp/master/certs/yourdomain.org/cert.pem`.
  Some webservers requires a separated chain and certificate. This is the certificate part, if you need it.

- **The combined certificate** at `~/.acmephp/master/certs/yourdomain.org/combined.pem`.
  Some webservers requires a single file combining the full-chain and the certificate private key.
  This is it, if you need it.

- **The certificate public key** at `~/.acmephp/master/private/yourdomain.org/public.pem`.
  You probably won't need it, but it's still available here.

You can now [configure your webserver](/documentation/getting-started/3-configure-webserver.html)!

## 5. Renew your certificate

If a certificate will expire soon, you will probably want to renew it. To do so, simply rerun the `request` command:

``` console
php acmephp.phar request yourdomain.org
```

This time, the command won't ask you anything (all the required informations were stored at the first request).
The certificate will be renewed.

Please note that there may be a limit of renewals per day or week depending on the configuration of the ACME server.
Let's Encrypt has this kind of limitation: renew only when required (one week before expiration for instance).

By default, the `request` command won't renew until one week before the expiration of the certificate. Therefore you
can put it as a CRON every day and it will renew only when required.

## 6. List the certificates handled by Acme PHP

Using the command `./acmephp.phar status`, you can see what certificates are handled by the Acme PHP client
and when they will expire. It's a useful tool to avoid expired certificates:

``` console
php acmephp.phar status

+---------------+----------------------------+---------------------+---------------------+----------------+
| Domain        | Issuer                     | Valid from          | Valid to            | Needs renewal? |
+---------------+----------------------------+---------------------+---------------------+----------------+
| acmephp.com   | Let's Encrypt Authority X3 | 2016-06-17 13:08:00 | 2016-09-15 13:08:00 | No             |
+---------------+----------------------------+---------------------+---------------------+----------------+
```

---------------------------------------------------------------------

Next: [Configure your webserver](/documentation/getting-started/3-configure-webserver.html)
