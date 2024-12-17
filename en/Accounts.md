
[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)

In order to request a CA (Certificate Authority) to sign your certificate, you may need to first create an account with that entity. Some CAs allow you to create accounts "on the fly". In that case ACMEd will create the account for you given information in acmed.toml.  Other Certificate Authorities will require that you first create an account, and include your API credentials.

This document will explain both cases.

---

## Accounts created “on the fly”

An account is, at a minimum, composed of a name of your choice and at least one contact method.

``` toml
[[account]]
name = "Mon compte"
contacts = [
    { mailto = "certs@example.org" },
]
```

---

### Contact Points

You can provide one or more contact methods that will be used by the certification authority to contact you as necessary. The nature, content and frequency of the notifications depend on each certification authority. The privacy policy of each CA will also vary.

The type of contact method allowed depends on both the support by the certification authority and by ACMEd. Only the mailto e-mail field is required.

Currently, ACMEd only supports `mail-to` e-mail.


## Pre-Registration

Accounts that require pre-registration require an Account Number and API-Key to identify you.

These elements must therefore be included in the `[[account]]` section. The account number is stored under `external_account.identifier` and the key as `external_account.key`.


``` toml
[[account]]
name = "MyAccount"
contacts = [
    { mailto = "certs@me.org" },
]
external_account.identifier = "kid-1"
external_account.key = "zWNDZM6eQGHWpSRTPal5eIUYFTu7EajVIoguysqZ9wG44nMEtx3MUAsUDkMTQ12W"
```

---

## Account Sharing

Although it is possible to share an account between different Certificate Authorities that use "on-the-fly" accounts, it is not recommended.

ACMEd usually launches one thread per Certificate Authority, so Co-mingling accounts reduces performance. In addition, some CA endpoints have identical url's for some steps in the registration process. This can cause issues when one CA inadvertently receives information intended for a completely different certificate, with unpredictable results.
