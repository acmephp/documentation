---
currentMenu: cli-introduction
---

# Acme PHP

Acme PHP is a simple yet very extensible command line tool that will let you get
and renew free HTTPS certificates.

It works with the [Let's Encrypt certificate authority](https://letsencrypt.org/),
using the [ACME protocol](https://github.com/letsencrypt/acme-spec). In a few steps,
you will be able to get a trusted certificate for any domain, freely and easily.

## Installation

Acme PHP is available as a single PHAR file to download on Github. For security purposes,
this PHAR file is signed using OpenSSL to ensure you are using a valid Acme PHP binary.

You can install it by running the following commands:

``` bash
cd ~
php -r "copy('https://github.com/acmephp/acmephp/releases/download/1.0.0-alpha10/acmephp.phar', 'acmephp.phar');"
php -r "copy('https://github.com/acmephp/acmephp/releases/download/1.0.0-alpha10/acmephp.phar.pubkey', 'acmephp.phar.pubkey');"
php acmephp.phar --version
```

If the last command display the Acme PHP version, you are ready to use Acme PHP.
[Start by getting your first certificate!](/documentation/cli/get-certificate.html)

## Use the latest development version

While we strongly recommand you to use a stable (or at least pre-release) version, you can also use the latest
development build if you need the latest features.

You can install it by running the following commands:

``` bash
cd ~
php -r "copy('https://acmephp.github.io/downloads/acmephp.phar', 'acmephp.phar');"
php -r "copy('https://acmephp.github.io/downloads/acmephp.phar.pubkey', 'acmephp.phar.pubkey');"
php acmephp.phar --version
```
