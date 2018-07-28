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

See the [SSL library documentation](/ssl/get-started.html) for an example of
generating a key pair.

## Usage examples

### Initialize the Acme PHP client

```php
<?php
$privateKey = new Acmephp\Ssl\PrivateKey($privatePemString);
$publicKey = new Acmephp\Ssl\PublicKey($publicPemString);
$keyPair = new Acmephp\Ssl\KeyPair($publicKey, $privateKey);

$secureHttpClient = new Acmephp\Core\Http\SecureHttpClient(
    $keyPair,
    new GuzzleHttp\Client(),
    new Acmephp\Core\Http\Base64SafeEncoder(),
    new Acmephp\Ssl\Parser\KeyParser(),
    new Acmephp\Ssl\Signer\DataSigner(),
    new Acmephp\Core\Http\ServerErrorHandler()
);

$client = new Acmephp\Core\AcmeClient($secureHttpClient, 'https://acme-v02.api.letsencrypt.org/directory');
```

### Register an account with your private key

``` php
<?php
$email = 'jane@example.com';
$client->register(null, $email);
```

### Authorize domains

```php
<?php
$domains = ['example.com', 'blog.example.com', 'www.example.com'];

$order = $client->requestOrder($domains);

foreach ($order->getAuthorizationsChallenges() as $domainKey => $authorizationChallenges) {
    foreach($authorizationChallenges as $challenge) {
        if ('http-01' === $authorizationChallenge->getType()) {
            // Complete the challenge with your own implementation or an Acme PHP
            // implementation in the \AcmePhp\Core\Challenge namespace.
        }
    }
}
```

### Request a new certificate

```php
<?php
$domain = 'example.com';
$alternativeNames = ['blog.example.com', 'www.example.com'];

$generator = new AcmePhp\Ssl\Generator\KeyPairGenerator();
$domainKeyPair = $generator->generateKeyPair();

$distinguishedName = new AcmePhp\Ssl\DistinguishedName(
    $domain,
    'FR' // country
    'Ile-de-France', // region
    'Paris', // locality
    'Example', // organization
    'Acme PHP', // organizational unit
    'acmephp@example.com', // email
    $alternativeNames
);

$certificateRequest = new AcmePhp\SSL\CertificateRequest($distinguishedName, $domainKeyPair);

$response = $client->finalizeOrder($order, $certificateRequest);

$certificate = $response->getCertificate();

var_dump($certificate->getPEM());
```

