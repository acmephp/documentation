---
currentMenu: core-introduction
---

# Acme PHP Core

Acme PHP Core is the ACME protocol implementation in PHP used by othere Acme PHP repositories
to interact with Let's Encrypt.

## When use Acme PHP Core?

You usually will want to use either [the Acme PHP CLI client](/documentation)
or [an implementation for your application framework](https://github.com/acmephp).

However, in some cases, you may want to manage your SSL certificates from the application itself.
In these cases, this library will be useful to you.

Acme PHP Core does nothing more than implementing the
[Let's Encrypt/ACME protocol](https://github.com/letsencrypt/acme-spec) : the generated SSL keys
and certificates are stored in memory and then given to your script. You are the one in charge
of storing them somewhere.
