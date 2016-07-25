---
currentMenu: cli-get-certificate
---

# Get started with Acme PHP

Now Acme PHP is available on your system (`./acmephp.phar --version` should display its version),
you can start requesting certificates for your domains using it.

## 1. Register your account private key on the Let's Encrypt/ACME server

The first thing we need to do is to generate a global private key (your "account key") that will be
used for future exchanges with Let's Encyrpt to prove your identity.

To do so, run the following command:

``` console
php acmephp.phar register youremail@example.com
```

This command will do four main things:
- create the `~/.acmephp` directory where all the data of Acme PHP will be stored
- create a default configuration file `~/.acmephp/acmephp.conf` that you may edit for advanced usages of Acme PHP
- generate a key pair for your account and store it in `~/.acmephp` (in `master` and `backup`)
- register your account key in the Let's Encrypt/ACME server, associating it with your e-mail adress

## 2. Verify you own the domain you want a certificate for

You can only request certificates for domains you own. To prove that, the Acme PHP client uses a HTTP
challenge.

The principle is quite simple:
- the Acme PHP client asks the Let's Encrypt server for a token
- you expose this token at a specific URL under the domain to check
- the Acme PHP client asks the server to check the URL for the token

Using this technique, you proved your server is responding behind the concerned domain.

**Note:** You only need to prove once that you own a domain (certificates renewals won't require it), as long as
you keep the same account key.

### 2a. Ask the server for the token

Run the `authorize` command with your domain:

``` console
php acmephp.phar authorize yourdomain.org

Loading account key pair...
Requesting an authorization token for domain yourdomain.org ...

The authorization token was successfully fetched!

Now, to prove you own the domain yourdomain.org and request certificates for this domain, follow these steps:

    1. Create a text file accessible on URL http://yourdomain.org/.well-known/acme-challenge/ukfMzbSUxNh2TfcR8ysFeV2gMT0nvkvGzSzo4siaIB8
       containing the following content:

       ukfMzbSUxNh2TfcR8ysFeV2gMT0nvkvGzSzo4siaIB8.K5qhJEjwWA-pgtp4BAeyI7kWWpWGFbxEZneBCZeTjSw

    2. Check in your browser that the URL http://yourdomain.org/.well-known/acme-challenge/ukfMzbSUxNh2TfcR8ysFeV2gMT0nvkvGzSzo4siaIB8 returns
       the authorization token above.

    3. Call the check command to ask the server to check your URL:

       php ./acmephp.phar check yourdomain.org
```

### 2b. Expose the token

As you may have understood, you now need to expose the token
`ukfMzbSUxNh2TfcR8ysFeV2gMT0nvkvGzSzo4siaIB8.K5qhJEjwWA-pgtp4BAeyI7kWWpWGFbxEZneBCZeTjSw` at the URL
`http://yourdomain.org/.well-known/acme-challenge/ukfMzbSUxNh2TfcR8ysFeV2gMT0nvkvGzSzo4siaIB8`.

To do so, usually a simple text file in the `.well-known/acme-challenge` directory under your web root
is enough, named as specified in the URL and containing the token.

Once that's done, make sure your URL works properly: try to access the URL
`http://yourdomain.org/.well-known/acme-challenge/ukfMzbSUxNh2TfcR8ysFeV2gMT0nvkvGzSzo4siaIB8` and check you see your
token.

If you do see that token, you can now ask the server to check it.

### 2c. Ask the server to check your URL

Run the `check` command with your domain:

``` console
php acmephp.phar check yourdomain.org

Loading account key pair...
Loading the authorization token for domain yourdomain.org ...
Requesting authorization check for domain yourdomain.org ...

The authorization check was successful!

You are now the proved owner of the domain yourdomain.org.
Please note that you won't need to prove it anymore as long as you keep the same account key pair.

You can now request a certificate for your domain:

   php ./acmephp.phar request yourdomain.org
```

If there is an issue, you will usually have a message describing the problem. If you are stuck, don't hesitate to
ask on [Github issues](https://github.com/acmephp/acmephp/issues).

## 3. Request a certificate for your domain

Finally! You are the proved owner of your domain, you can now request and renew a certificate for this domain.

To do so, simply run the `request` command:

``` console
php acmephp.phar request yourdomain.org
```

This command will ask you required informations for the certificate, request it to the server and store it in
the `~/.acmephp` storage directory.

6 files will be created in the storage directory:
  
- **The full-chain certificate** at `~/.acmephp/master/certs/yourdomain.org/fullchain.pem`.
  This file is the certificate itself. You probably want to use this file in your webserver configurationas it
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

## 4. List your certificates and their status

Using the command `./acmephp.phar status`, you can see what certificates are handled by the Acme PHP client
and when they will expire. It's a useful tool to avoid expired certificates:

``` console
php acmephp.phar status

+------------------------------+----------------------------+---------------------+---------------------+----------------+
| Domain                       | Issuer                     | Valid from          | Valid to            | Needs renewal? |
+------------------------------+----------------------------+---------------------+---------------------+----------------+
| acmephp.titouangalopin.com   | Let's Encrypt Authority X3 | 2016-06-17 13:08:00 | 2016-09-15 13:08:00 | No             |
+------------------------------+----------------------------+---------------------+---------------------+----------------+
```

## 5. Renew a certificate

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

Next: [Configure your webserver](/documentation/cli/webserver.html)
