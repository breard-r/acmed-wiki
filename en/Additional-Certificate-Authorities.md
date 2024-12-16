[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)


# Table of Contents
* [Buypass Go SSL](#Buypass-Go-SSL)
* [GoDaddy](#GoDaddy)
* [Google Cloud](#Google-Cloud)
* [Let's Encrypt](#Let's-Encrypt)
* [Sectigo](#Sectigo)
* [SSL.com](#SSL.com)
* [ZeroSSL](#ZeroSSL)

This page contains a non-exhaustive list of certification authorities in alphabetical order.

All examples intentionally refuse to accept the `terms of use`. It is your responsibility to read these terms of use and then accept them by setting `tos_agreed` to true.

---

## Buypass Go SSL

|  | Summary |
|--:|:--|
| Product: | https://www.buypass.com/ssl/products/acme |
| Country of Origin: | Norway |
| External Account Required: | No |
| Documentation:| https://community.buypass.com/category/go-ssl-acme |
| Entry Points: | |
| Test Environment: | https://api.test4.buypass.no/acme/directory |
| Production: | https://api.buypass.com/acme/directory |
| Rate Limits: | Unknown (but certificate limit) |

### Example Configuration:

``` toml
[[endpoint]]
name = "Buypass Go SSL production"
url = "https://api.buypass.com/acme/directory"
tos_agreed = false

[[endpoint]]
name = "Buypass Go SSL test"
url = "https://api.test4.buypass.no/acme/directory"
tos_agreed = false
```
---

## GoDaddy

|  | Summary |
|--:|:--|
| Product:| https://www.godaddy.com/help/set-up-my-ssl-certificate-with-acme-40393|
| Country of Origin:| United States of America|
| External Account Required:| Yes|
| Documentation:| https://www.godaddy.com/help/set-up-my-ssl-certificate-with-acme-40393|
| Endpoints:| Unknown|
| Rate Limits:| Unknown|

---

## Google Cloud

|  | Summary |
|--:|:--|
| Product:| https://cloud.google.com/blog/products/identity-security/automate-public-certificate-lifecycle-management-via--acme-client-api|
| Country of Origin:| United States of America|
| External Account Required:| Yes|
| Documentation:| https://cloud.google.com/blog/products/identity-security/automate-public-certificate-lifecycle-management-via--acme-client-api|
| Endpoints input:||
| Test environment:| https://dv.acme-v02.test-api.pki.goog/directory|
| Production:| https://dv.acme-v02.api.pki.goog/directory|
| Rate limits:| unknown|

### Sample configuration:

``` toml
[[account]]
# [...]
external_account.identifier = "ACCOUNT-KEY"
external_account.key = "HMAC-KEY"

[[endpoint]]
name = "Google Cloud production"
url = "https://dv.acme-v02.api.pki.goog/directory"
tos_agreed = false

[[endpoint]]
name = "Google Cloud test"
url = "https://dv.acme-v02.test-api.pki.goog/directory"
tos_agreed = false
```
---

## Let's Encrypt

|  | Summary |
|--:|:--|
| Product:| https://letsencrypt.org|
| Country of Origin:| United States of America|
| External Account Required:| No|
| Documentation:| https://letsencrypt.org/docs|
| Entry Points:||
| Staging:| https://acme-staging-v02.api.letsencrypt.org/directory|
| Production:| https://acme-v02.api.letsencrypt.org/directory|
| Rate Limits:| Yes|

### Sample Configuration:

``` toml
[[rate-limit]]
name = "Let's Encrypt Overall Requests limit"
number = 20
period = "1s"

[[endpoint]]
name = "Let's Encrypt production"
url = "https://acme-v02.api.letsencrypt.org/directory"
rate_limits = ["Let's Encrypt Overall Requests limit"]
tos_agreed = false

[[endpoint]]
name = "Let's Encrypt staging"
url = "https://acme-staging-v02.api.letsencrypt.org/directory"
rate_limits = ["Let's Encrypt Overall Requests limit"]
tos_agreed = false
```
---

## Sectigo
  (formerly Comodo CA)

|  | Summary |
|--:|:--|
| Product:| https://sectigo.com/resource-library/sectigos-acme-automation|
| Country of Origin:| United States of America|
| External Account Required:| Yes|
| Documentation:| https://docs.sectigo.com/scm/sectigo-nginx-lego-certificate-management/overview.html|
| Endpoints:| |
| * DV Certificate:| https://acme.sectigo.com/v2/DV|
| * OV Certificate:| https://acme.sectigo.com/v2/OV|
| * EV Certificate:| https://acme.sectigo.com/v2/EV|
| * OV Certificate (Geant):| https://acme.sectigo.com/v2/GEANTOV|
| Rate limits:| unknown|

### Example configuration:

``` toml
[[account]]
# [...]
external_account.identifier = "ACCOUNT-KEY"
external_account.key = "HMAC-KEY"

[[endpoint]]
name = "Sectigo DV"
url = "https://acme.sectigo.com/v2/DV"
tos_agreed = false

[[endpoint]]
name = "Sectigo OV"
url = "https://acme.sectigo.com/v2/OV"
tos_agreed = false

[[endpoint]]
name = "Sectigo EV"
url = "https://acme.sectigo.com/v2/EV"
tos_agreed = false

[[endpoint]]
name = "Sectigo OV - Giant"
url = "https://acme.sectigo.com/v2/GEANTOV"
tos_agreed = false
```
---

## SSL.com

|  | Summary |
|--:|:--|
| Product:| https://www.ssl.com/certificates/|
| Country of Origin:| United States of America|
| External Account Required:| Yes, but advertised as No|
| Documentation:| https://www.ssl.com/guide/ssl-tls-certificate-issuance-and-revocation-with-acme/|
| Endpoints:||
| Production (RSA key):| https://acme.ssl.com/sslcom-dv-rsa|
| Production (ECDSA key):| https://acme.ssl.com/sslcom-dv-ecc|
| Rate Limits:| Unknown|

### Sample Configuration:

``` toml
[[account]]
# [...]
external_account.identifier = "ACCOUNT-KEY"
external_account.key = “HMAC-KEY”

[[endpoint]]
name = "SSL.com DV RSA"
url = "https://acme.ssl.com/sslcom-dv-rsa"
tos_agreed = false

[[endpoint]]
name = "SSL.com DV ECC"
url = "https://acme.ssl.com/sslcom-dv-ecc"
tos_agreed = false
```
---

## ZeroSSL

|  | Summary |
|--:|:--|
| Product:| https://zerossl.com/features/acme/|
| Country of Origin:| Austria|
| External Account Required:| Yes|
| Documentation:| https://zerossl.com/documentation/acme/|
| Endpoints:| https://acme.zerossl.com/v2/DV90|
| Rate Limits:| Unknown|

### Sample Configuration:

``` toml
[[account]]
# [...]
external_account.identifier = "ACCOUNT-KEY"
external_account.key = "HMAC-KEY"

[[endpoint]]
name = "ZeroSSL production"
url = "https://acme.zerossl.com/v2/DV90"
tos_agreed = false
```
