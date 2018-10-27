---
currentMenu: core-get-started
---

# Get started

## Installation

You will need the PHP OpenSSL extension to use Acme PHP Core.

Install this library using Composer:

```
composer require acmephp/core
```

## Usage

---
currentMenu: core-get-started
---

# Get started

## Installation

You will need the PHP OpenSSL extension to use Acme PHP Core.

Install this library using Composer:

```
composer require acmephp/core
```

## Usage examples

### Initialize dependencies

``` php
<?php
use AcmePhp\Core\Http\ {
    Base64SafeEncoder,
    SecureHttpClientFactory,
    ServerErrorHandler
};
use AcmePhp\Ssl\ {
    KeyPair,
    PrivateKey,
    PublicKey
};
use AcmePhp\Ssl\Parser\KeyParser;
use AcmePhp\Ssl\Signer\DataSigner;
use GuzzleHttp\Client as GuzzleHttpClient;

$httpClient = new GuzzleHttpClient();
$base64Encoder = new Base64SafeEncoder();
$keyParser = new KeyParser();
$dataSigner = new DataSigner();
$serverErrorHandler = new ServerErrorHandler();

$secureHttpClientFactory = new SecureHttpClientFactory(
    $httpClient,
    $base64Encoder,
    $keyParser,
    $dataSigner,
    $serverErrorHandler
);

var_dump($secureHttpClientFactory);
```

### Generate keys

``` php
<?php
use AcmePhp\Ssl\Generator\KeyPairGenerator;

$publicKeyPath = realpath(dirname(__FILE__) . '/path/to/keys') . '/account.pub.pem';
$privateKeyPath = realpath(dirname(__FILE__) . '/path/to/keys') . '/account.pem';

$publicKeyPem = file_get_contents($publicKeyPath);
$privateKeyPem = file_get_contents($privateKeyPath);

if ($publicKeyPem === false && $privateKeyPem === false) {
    $keyPairGenerator = new KeyPairGenerator();

    $keyPair = $keyPairGenerator->generateKeyPair();

    file_put_contents($publicKeyPath, $keyPair->getPublicKey()->getPEM());
    file_put_contents($privateKeyPath, $keyPair->getPrivateKey()->getPEM());
} else {
    $publicKey = new PublicKey($publicKeyPem);
    $privateKey = new PrivateKey($privateKeyPem);

    $keyPair = new KeyPair($publicKey, $privateKey);
}

var_dump($keyPair);
```

### Set up client
``` php
<?php
use AcmePhp\Core\AcmeClient;

$secureHttpClient = $secureHttpClientFactory->createSecureHttpClient($keyPair);
// Important, change to production LE directory for real certs!
$acmeClient = new AcmeClient($secureHttpClient, 'https://acme-staging-v02.api.letsencrypt.org/directory');
var_dump($acmeClient);
```

### Create new account
``` php
<?php
$acmeClient->registerAccount(null, 'testing@tester.com');
```

### Request authorization
``` php
<?php
$authorizationChallenges = $acmeClient->requestAuthorization('mydomain.com');
var_dump($authorizationChallenges);
// Stage your challenge response via DNS or HTTP before making the next call
var_dump($acmeClient->challengeAuthorization($authorizationChallenges[0]));
```

### Generate CSR and private key
``` php
<?php
$dn = new DistinguishedName('mydomain.com');

$keyPairGenerator = new KeyPairGenerator();

// Make a new key pair. We'll keep the private key as our cert
// key
$domainKeyPair = $keyPairGenerator->generateKeyPair();

// This is the private key
var_dump($domainKeyPair->getPrivateKey()->getPem());

// Generate CSR
$csr = new CertificateRequest($dn, $domainKeyPair);
```

### Request certificate
``` php
<?php
$certificateResponse = $acmeClient->requestCertificate('mydomain.com', $csr);

// This is the certificate (public key)
var_dump($certificateResponse->getCertificate()->getPem());

// For LetsEncrypt, you will need the intermediate too
var_dump($certificateResponse->getCertificate()->getIssuerCertificate()->getPEM());
```
