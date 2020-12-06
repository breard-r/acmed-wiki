
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


## ZeroSSL

- Produit : [https://zerossl.com/features/acme/](https://zerossl.com/features/acme/)
- Pays d'origine : Autriche
- Compte externe requis : oui
- Documentation : [https://zerossl.com/documentation/acme/](https://zerossl.com/documentation/acme/)
- Point d'entrée : [https://acme.zerossl.com/v2/DV90](https://acme.zerossl.com/v2/DV90)
- Limites de débit : inconnues

Exemple de configuration :

```
[[endpoint]]
name = "ZeroSSL production"
url = "https://acme.zerossl.com/v2/DV90"
tos_agreed = false
```
