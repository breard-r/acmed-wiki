
[//]: # (Copyright 2019-2020 Rodolphe Bréard <rodolphe@breard.tf>)

[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)


Afin de pouvoir demander à une autorité de certification de signer votre certificat, il est nécessaire d'avoir un compte chez cette dernière. Certaines autorités de certification permettent de créer des comptes « à la volée », ce qui est alors automatisé par ACMEd d'après les paramètres que vous aurez indiqué. D'autres autorités de certification nécessitent que vous ayez préalablement créé un compte, souvent en s'enregistrant sur leur site web. Dans ce chapitre nous aborderons les deux cas.


## Comptes créés « à la volée »

Un compte est, à minima, composé d'un nom de votre choix et d'au moins un moyen de contact. Le nom sera utilisé par ACMEd uniquement, l'autorité de certification n'en aura pas connaissance.

``` toml
[[account]]
name = "Mon compte"
contacts = [
    { mailto = "certs@example.org" },
]
```

### Moyens de contact

Il est possible de définir un ou plusieurs moyens de contact qui seront utilisés par l'autorité de certification afin que cette dernière puisse vous envoyer différente notification. La nature, le contenu et la fréquence des notifications dépendent de chaque autorité de certification. De la même manière, chacune disposera de sa propre politique de protection des données à caractère personnel.

Le type de moyen de contact autorisé dépends à la fois du support par l'autorité de certification et par ACMEd. Seul le type [mailto](https://fr.wikipedia.org/wiki/Mailto) (sans `hfields` et avec maximum une `addr-spec` dans le `to`) est obligatoirement supporté par les AC.

Actuellement, ACMEd ne supporte que le type `mailto`.


## Inscription préalable

Les comptes nécessitant une inscription préalable fonctionnent exactement comme les comptes créés « à la volée », à une différence près. Dans ce cas d'usage, l'autorité de certification doit vous fournir un identifiant et une clé. Ces éléments sont donc à ajouter à la définition du compte à l'aide des directives `external_account.identifier` pour l'identifiant et `external_account.key` pour la clé.

``` toml
[[account]]
name = "Mon compte"
contacts = [
    { mailto = "certs@example.org" },
]
external_account.identifier = "kid-1"
external_account.key = "zWNDZM6eQGHWpSRTPal5eIUYFTu7EajVIoguysqZ9wG44nMEtx3MUAsUDkMTQ12W"
```


## Compte partagé entre plusieurs autorités de certification

ACMEd vous permet d'utiliser un seul objet de compte sur plusieurs points d'entrée. Dans la pratique, ceci se traduira par la création d'un compte indépendant sur chacune des autorités de certification. En cas de modification des paramètres du compte, ACMEd répercutera ces changements sur chacune de ces autorités de certification.

Cette pratique, bien qu'autorisée, est cependant déconseillée car elle cause plusieurs problèmes.

Afin de gagner en performances, ACMEd lance un fil d'exécution (thread) par point d'entrée. Or, un compte ne pouvant être utilisé que par un seul fil d'exécution à la fois. Si plusieurs fils d'exécution ont besoin d'utiliser le même compte pour renouveler un certificat, ils ne pourront le faire que un par un, ce qui engendre un temps d'attente et donc une perte de performances non négligeable.

Un autre problème se pose si deux points d'entrées, de noms différents mais utilisant la même URL, sont utilisés avec un unique compte. ACMEd considérant les deux points d'entrée comme différents, il ne s'attend pas à ce que la création ou la modification d'un compte sur l'un impacte le compte de l'autre. Le comportement d'ACMEd n'est alors pas défini et est susceptible d'évoluer au fil du temps. Il est fortement recommandé de ne pas se retrouver dans ce cas de figure.
