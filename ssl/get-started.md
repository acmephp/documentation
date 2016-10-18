---
currentMenu: ssl-get-started
---

# Get started

## Installation

You will need the PHP OpenSSL extension to use Acme PHP SSL.

Install this library using Composer:

``` bash
composer require acmephp/ssl
```

## Usage examples

### Sign data with a private key

``` php
<?php
$signer = new AcmePhp\Ssl\Signer\DataSigner();
$privateKey = new AcmePhp\Ssl\PrivateKey($pemString);

var_dump($signer->signData($customData, $privateKey));
```

### Parse a SSL certificate to extract its metadata

``` php
<?php
$parser = new AcmePhp\Ssl\Parser\CertificateParser();

$rawCertificate = new AcmePhp\Ssl\Certificate($pemString);
$parsedCertificate = $parser->parse($rawCertificate);

var_dump($parsedKey->getSource());
var_dump($parsedKey->getSubject());
var_dump($parsedKey->getIssuer());
var_dump($parsedKey->isSelfSigned());
var_dump($parsedKey->getValidFrom());
var_dump($parsedKey->getValidTo());
var_dump($parsedKey->getSerialNumber());
var_dump($parsedKey->getSubjectAlternativeNames());
```

### Generate a key pair (a private key and its associated public key)

``` php
<?php
$generator = new AcmePhp\Ssl\Generator\KeyPairGenerator();

$keyPair = $generator->generateKeyPair();

var_dump($keyPair->getPublicKey()->getPEM());
var_dump($keyPair->getPrivateKey()->getPEM());
```

### Parse a key to extract its metadata

``` php
<?php
$parser = new AcmePhp\Ssl\Parser\KeyParser();

$rawKey = new AcmePhp\Ssl\PrivateKey($pemString);
$parsedKey = $parser->parse($rawKey);

var_dump($parsedKey->getSource());
var_dump($parsedKey->getKey());
var_dump($parsedKey->getBits());
var_dump($parsedKey->getType());
var_dump($parsedKey->getDetails());
```

## API documentation

### SSL entities

This library provides the following SSL entities:

- **PrivateKey**: a private key
- **PublicKey**: a public key
- **ParsedKey**: data resulting of the decoding of a key (public or private)
- **KeyPair**: a couple of public and private key
- **Certificate**: a PEM certificate string (an encoded certificate)
- **ParsedCertificate**: data resulting of the decoding of a parsed certificate
- **DistinguishedName**: required data used to generate a Certificate Request Signing
- **CertificateRequest**: required data used to request a certificate
- **CertificateResponse**: the result of a certificate request

These entities are the objects you will receive from the services provided by this library.

### Generators

Generators are under `AcmePhp\Ssl\Generator` namespace.

- **KeyPairGenerator** generates a **KeyPair** entity (using OpenSSL functions)

### Parsers

Parsers are under `AcmePhp\Ssl\Parser` namespace.

- **CertificateParser** parses certificates (**Certificate** entities) and return **ParsedCertificate** entities
- **KeyParser** parses keys (**PrivateKey or PublicKey** entities) and return **ParsedKey** entities

### Signers

Signers are under `AcmePhp\Ssl\Signer` namespace.

- **CertificateRequestSigner** signs Certificate requests (CSR)
- **DataSigner** signs custom data using a private key

