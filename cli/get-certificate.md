---
currentMenu: cli-get-certificate
---

# Get started with Acme PHP

Now Acme PHP is available on your system (`./acmephp.phar --version` should display its version),
you can start requesting certificates for your domains using it.

## 1. Register your account private key on the Let's Encrypt/ACME server

The first thing we need to do is to generate a global private key (your "account key") that will be
used for future exchanges with Let's Encyrpt to proove your identity.

To do so, run the following command:

``` console
./acmephp.phar register youremail@example.com

# If you are not using Let's Encrypt, you may need to change the --agreement option
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
./acmephp.phar authorize yourdomain.org

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
./acmephp.phar check yourdomain.org

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
./acmephp.phar request yourdomain.org
```

This command will ask you required informations for the certificate, request it to the server and store it in
the `~/.acmephp` storage directory.

5 files will be created in the storage directory:

- `~/.acmephp/master/private/yourdomain.org/private.pem` contains your domain private key (required in many cases).
- `~/.acmephp/master/certs/yourdomain.org/cert.pem` contains only your certificate, without the issuer certificate.
  It may be useful in certains cases but you will probably not need it (use fullchain.pem instead).
- `~/.acmephp/master/certs/yourdomain.org/chain.pem` contains the issuer certificate chain (its certificate, the
  certificate of its issuer, the certificate of the issuer of its issuer, etc.). Your certificate is
  not present in this file.
- `~/.acmephp/master/certs/yourdomain.org/fullchain.pem` contains your certificate AND the issuer certificate chain.
  You most likely will use this file in your webserver.
- `~/.acmephp/master/certs/yourdomain.org/combined.pem` contains the fullchain AND your domain private key (some
  webservers expect this format such as haproxy).

You can continue in the documentation [by configuring your webserver](/documentation/cli/webserver.html).
