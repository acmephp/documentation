---
currentMenu: guides-dns-challenge
---

# Use the DNS challenge to prove you own a domain

By default, Acme PHP will use a HTTP challenge to prove you own a domain: you will create a file the ACME server
will access to verify the token you exposed.

However it is possible to use DNS to check your ownership over a domain: instead of exposing a file, you will expose
a TXT field.

## Get the token to expose

To use DNS challenge, run the authorize command with the `--solver dns` argument:

``` console
$ php acmephp.phar authorize --solver dns yourdomain.org

The authorization token was successfully fetched!
    Add the following TXT record to your DNS zone
        Domain: _acme-challenge.yourdomain.org.
        TXT value: zExIQaKdxouvZ6rbpYApsawiwgINj3zbmXym-KhuNsA
        
    Wait for the propagation before moving to the next step
    Tips: Use the following command to check the propagation

        host -t TXT _acme-challenge.yourdomain.org.

Then, you can ask to the CA to check the challenge!
    Call the check command to ask the server to check your URL:

    php acmephp.phar check -s dns yourdomain.org
```

## Add a DNS TXT field for the token

You have now to create a DNS TXT field for the token:

```
Add the following TXT record to your DNS zone
    Domain: _acme-challenge.yourdomain.org.
    TXT value: zExIQaKdxouvZ6rbpYApsawiwgINj3zbmXym-KhuNsA
```

## Ask for the server to check your proof

Once done, wait a few minutes for DNS propagation. When the field is ready, run
the check command with the `--solver dns` argument:

``` console
php acmephp.phar check --solver dns yourdomain.org
```
