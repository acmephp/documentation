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

### 1. Create a secure HTTP client

A SecureHttpClient is a Guzzle HTTP client wrapper to send requests signed with the account KeyPair.

``` php
<?php

use AcmePhp\Core\Http\Base64SafeEncoder;
use AcmePhp\Core\Http\SecureHttpClientFactory;
use AcmePhp\Core\Http\ServerErrorHandler;
use AcmePhp\Ssl\KeyPair;
use AcmePhp\Ssl\PrivateKey;
use AcmePhp\Ssl\PublicKey;
use AcmePhp\Ssl\Parser\KeyParser;
use AcmePhp\Ssl\Signer\DataSigner;
use GuzzleHttp\Client as GuzzleHttpClient;

$secureHttpClientFactory = new SecureHttpClientFactory(
    new GuzzleHttpClient(),
    new Base64SafeEncoder(),
    new KeyParser(),
    new DataSigner(),
    new ServerErrorHandler()
);

// $accountKeyPair instance of KeyPair
$secureHttpClient = $secureHttpClientFactory->createSecureHttpClient($accountKeyPair);

// See AcmePhp\Core\Http\SecureHttpClient for all available methods.
```

### 2. Generate a key pair if needed

If you don't already have a KeyPair, you can generate one:

``` php
<?php

use AcmePhp\Ssl\Generator\KeyPairGenerator;

$publicKeyPath = '/custom/path/to/keys/account.pub.pem';
$privateKeyPath = '/custom/path/to/keys/account.pem';

if (!file_exists($privateKeyPath)) {
    $keyPairGenerator = new KeyPairGenerator();
    $keyPair = $keyPairGenerator->generateKeyPair();

    file_put_contents($publicKeyPath, $keyPair->getPublicKey()->getPEM());
    file_put_contents($privateKeyPath, $keyPair->getPrivateKey()->getPEM());
} else {
    $publicKey = new PublicKey(file_get_contents($publicKeyPath));
    $privateKey = new PrivateKey(file_get_contents($privateKeyPath));

    $keyPair = new KeyPair($publicKey, $privateKey);
}
```

### 3. Set up the ACME client

``` php
<?php

use AcmePhp\Core\AcmeClient;

$secureHttpClient = $secureHttpClientFactory->createSecureHttpClient($keyPair);

// Important, change to production LE directory for real certs!
$acmeClient = new AcmeClient($secureHttpClient, 'https://acme-staging-v02.api.letsencrypt.org/directory');
```

See `AcmePhp\Core\AcmeClientInterface` and `AcmePhp\Core\AcmeClientV2Interface` for detailed
explainations of each methods of the client.

### 4. Create new account

``` php
<?php

// registerAccount($agreement = null, $email = null)
$acmeClient->registerAccount(null, 'testing@tester.com');
```

### 5. Request authorization

``` php
<?php

// This will return a list of challenges that you can use to prove you own the domain.
$authorizationChallenges = $acmeClient->requestAuthorization('mydomain.com');
var_dump($authorizationChallenges);

// You need to stage  your challenge response via DNS or HTTP before making the next call:
// $acmeClient->challengeAuthorization($authorizationChallenges[0])
```

### 6. Generate CSR and private key

``` php
<?php

$dn = new DistinguishedName('mydomain.com');

$keyPairGenerator = new KeyPairGenerator();

// Make a new key pair. We'll keep the private key as our cert key
$domainKeyPair = $keyPairGenerator->generateKeyPair();

// This is the private key
var_dump($domainKeyPair->getPrivateKey()->getPem());

// Generate CSR
$csr = new CertificateRequest($dn, $domainKeyPair);
```

### 7. Request certificate

``` php
<?php

$certificateResponse = $acmeClient->requestCertificate('mydomain.com', $csr);

// This is the certificate (public key)
var_dump($certificateResponse->getCertificate()->getPem());

// For Let's Encrypt, you will need the intermediate too
var_dump($certificateResponse->getCertificate()->getIssuerCertificate()->getPEM());
```
