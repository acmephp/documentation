---
currentMenu: cli-introduction
---

# Acme PHP

Acme PHP is a simple yet very extensible command line tool that will let you get
and renew free HTTPS certificates.

It works with the [Let's Encrypt certificate authority](https://letsencrypt.org/),
using the [ACME protocol](https://github.com/letsencrypt/acme-spec). In a few steps,
you will be able to get a trusted certificate for any domain, freely and easily.

Acme PHP also proposes advanced features such as:

- [automatic renewal](/documentation/cli/automatic-renewal.html) (you won't forget to renew your certificates anymore!)
- post-generate actions (for instance to [copy your certificates over the network](/documentation//cli/copy-network.html))
- [monitoring](/documentation/cli/monitoring.html) (to be alerted if an automatic renewal failed)

## Installation

Acme PHP is available as a single PHAR file to download on Github. However, for security purposes,
this PHAR file is signed using OpenSSL to ensure you are using a valid Acme PHP binary.

We recommand you to put the PHAR file on the root of your home directory as it is a tool global to
your system.

You can run the following commands:

``` bash
cd ~
php -r "copy('https://github.com/acmephp/acmephp/releases/download/1.0.0-alpha8/acmephp.phar', 'acmephp.phar');"
php -r "copy('https://github.com/acmephp/acmephp/releases/download/1.0.0-alpha8/acmephp.phar.pubkey', 'acmephp.phar.pubkey');"
chmod a+x acmephp.phar
./acmephp.phar --version
```

If the last command display the Acme PHP version, you are ready to use Acme PHP.
[Start by getting your first certificate!](/documentation/cli/get-certificate.html)

## Use the lastest development version

While we strongly recommand you to use a stable (or at least pre-release) version, you can also use the lastest
development build if you need latest features.

To do so, you can either run `./acmephp.phar self-update --dev` if you already have the client or download
the development version directly using the following commands:

``` bash
cd ~
php -r "copy('https://acmephp.github.io/downloads/acmephp.phar', 'acmephp.phar');"
php -r "copy('https://acmephp.github.io/downloads/acmephp.phar.pubkey', 'acmephp.phar.pubkey');"
chmod a+x acmephp.phar
./acmephp.phar --version
```

**Note**: the version you will see is not the right version (it will be the last pre-release version), but
that's normal.
