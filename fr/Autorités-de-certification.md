
[//]: # (Copyright 2019-2020 Rodolphe Bréard <rodolphe@breard.tf>)

[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)

Cette page regroupe une liste non-exhaustive des différentes autorités de certification. Ces dernières sont classées par ordre alphabétique.

Les exemples de configuration refusent systématiquement les conditions d'utilisation de ces services. Afin de pouvoir les utiliser, il est de votre responsabilité de lire ces conditions d'utilisation puis de les accepter en passant `tos_agreed` à `true`.

## Buypass Go SSL

- Produit : [https://www.buypass.com/ssl/products/acme](https://www.buypass.com/ssl/products/acme)
- Pays d'origine : Norvège
- Compte externe requis : non
- Documentation : [https://community.buypass.com/category/go-ssl-acme](https://community.buypass.com/category/go-ssl-acme)
- Points d'entrée :
  * Environnement de test : [https://api.test4.buypass.no/acme/directory](https://api.test4.buypass.no/acme/directory)
  * Production : [https://api.buypass.com/acme/directory](https://api.buypass.com/acme/directory)
- Limites de débit : inconnues ([mais limitation du nombre de certificats](https://community.buypass.com/t/h7hmbsh/rate-limits))

Exemple de configuration :

```
[[endpoint]]
name = "Buypass Go SSL production"
url = "https://api.buypass.com/acme/directory"
tos_agreed = false

[[endpoint]]
name = "Buypass Go SSL test"
url = "https://api.test4.buypass.no/acme/directory"
tos_agreed = false
```


## GoDaddy

- Produit : [https://www.godaddy.com/help/set-up-my-ssl-certificate-with-acme-40393](https://www.godaddy.com/help/set-up-my-ssl-certificate-with-acme-40393)
- Pays d'origine : États-Unis d'Amérique
- Compte externe requis : oui
- Documentation : [https://www.godaddy.com/help/set-up-my-ssl-certificate-with-acme-40393](https://www.godaddy.com/help/set-up-my-ssl-certificate-with-acme-40393)
- Points d'entrée : inconnus
- Limites de débit : inconnues


## Google Cloud

- Produit : [https://cloud.google.com/blog/products/identity-security/automate-public-certificate-lifecycle-management-via--acme-client-api](https://cloud.google.com/blog/products/identity-security/automate-public-certificate-lifecycle-management-via--acme-client-api)
- Pays d'origine : États-Unis d'Amérique
- Compte externe requis : oui
- Documentation : [https://cloud.google.com/blog/products/identity-security/automate-public-certificate-lifecycle-management-via--acme-client-api](https://cloud.google.com/blog/products/identity-security/automate-public-certificate-lifecycle-management-via--acme-client-api)
- Points d'entrée :
  * Environnement de test : [https://dv.acme-v02.test-api.pki.goog/directory](https://dv.acme-v02.test-api.pki.goog/directory)
  * Production : [https://dv.acme-v02.api.pki.goog/directory](https://dv.acme-v02.api.pki.goog/directory)
- Limites de débit : inconnues

```
[[endpoint]]
name = "Google Cloud production"
url = "https://dv.acme-v02.api.pki.goog/directory"
tos_agreed = false

[[endpoint]]
name = "Google Cloud test"
url = "https://dv.acme-v02.test-api.pki.goog/directory"
tos_agreed = false
```


## Let's Encrypt

- Produit : [https://letsencrypt.org/](https://letsencrypt.org/)
- Pays d'origine : États-Unis d'Amérique
- Compte externe requis : non
- Documentation : [https://letsencrypt.org/docs/](https://letsencrypt.org/docs/)
- Points d'entrée :
  * Environnement de test : [https://acme-staging-v02.api.letsencrypt.org/directory](https://acme-staging-v02.api.letsencrypt.org/directory)
  * Production : [https://acme-v02.api.letsencrypt.org/directory](https://acme-v02.api.letsencrypt.org/directory)
- Limites de débit : [oui](https://letsencrypt.org/docs/rate-limits/)

Exemple de configuration :

```
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


## Sectigo (anciennement Comodo CA)

- Produit : [https://sectigo.com/resource-library/sectigos-acme-automation](https://sectigo.com/resource-library/sectigos-acme-automation)
- Pays d'origine : États-Unis d'Amérique
- Compte externe requis : oui
- Documentation : [https://docs.sectigo.com/scm/sectigo-nginx-lego-certificate-management/overview.html](https://docs.sectigo.com/scm/sectigo-nginx-lego-certificate-management/overview.html)
- Points d'entrée :
  * Certificat DV : [https://acme.sectigo.com/v2/DV](https://acme.sectigo.com/v2/DV)
  * Certificat OV: [https://acme.sectigo.com/v2/OV](https://acme.sectigo.com/v2/OV)
  * Certificat EV : [https://acme.sectigo.com/v2/EV](https://acme.sectigo.com/v2/EV)
  * Certificat OV (Geant) : [https://acme.sectigo.com/v2/GEANTOV](https://acme.sectigo.com/v2/GEANTOV)
- Limites de débit : inconnues

Exemple de configuration :

```
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
name = "Sectigo OV - Geant"
url = "https://acme.sectigo.com/v2/GEANTOV"
tos_agreed = false
```


## SSL.com

- Produit : [https://www.ssl.com/certificates/](https://www.ssl.com/certificates/)
- Pays d'origine : États-Unis d'Amérique
- Compte externe requis : oui, mais annoncé comme non
- Documentation : [https://www.ssl.com/guide/ssl-tls-certificate-issuance-and-revocation-with-acme/](https://www.ssl.com/guide/ssl-tls-certificate-issuance-and-revocation-with-acme/)
- Points d'entrée :
  * Production (clé RSA) : [https://acme.ssl.com/sslcom-dv-rsa](https://acme.ssl.com/sslcom-dv-rsa)
  * Production (clé ECDSA) : [https://acme.ssl.com/sslcom-dv-ecc](https://acme.ssl.com/sslcom-dv-ecc)
- Limites de débit : inconnues

Exemple de configuration :

```
[[account]]
# [...]
external_account.identifier = "ACCOUNT-KEY"
external_account.key = "HMAC-KEY"

[[endpoint]]
name = "SSL.com DV RSA"
url = "https://acme.ssl.com/sslcom-dv-rsa"
tos_agreed = false

[[endpoint]]
name = "SSL.com DV ECC"
url = "https://acme.ssl.com/sslcom-dv-ecc"
tos_agreed = false
```


## ZeroSSL

- Produit : [https://zerossl.com/features/acme/](https://zerossl.com/features/acme/)
- Pays d'origine : Autriche
- Compte externe requis : oui
- Documentation : [https://zerossl.com/documentation/acme/](https://zerossl.com/documentation/acme/)
- Point d'entrée : [https://acme.zerossl.com/v2/DV90](https://acme.zerossl.com/v2/DV90)
- Limites de débit : inconnues

Exemple de configuration :

```
[[account]]
# [...]
external_account.identifier = "ACCOUNT-KEY"
external_account.key = "HMAC-KEY"

[[endpoint]]
name = "ZeroSSL production"
url = "https://acme.zerossl.com/v2/DV90"
tos_agreed = false
```
