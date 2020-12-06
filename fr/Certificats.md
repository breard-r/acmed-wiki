
[//]: # (Copyright 2019-2020 Rodolphe Bréard <rodolphe@breard.tf>)

[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)


Afin d'obtenir un certificat signé par une autorité de certification, il vous faut définir ce que vous souhaitez. Pour cela, il vous faut indiquer, à minima :

- le [point d'entrée](Points-d'entrée-et-limites-de-débit) à utiliser ;
- le [compte utilisateur](Comptes) à utiliser sur ce point d'entrée ;
- les identifiants à valider (noms de domaine, adresses IP, etc) ainsi que le challenge à utiliser ;
- les [hooks](Hooks-et-groupes) utilisés pour répondre aux défis.


``` toml
[[certificate]]
endpoint = "Example CA"
account = "Mon compte"
identifiers = [
    { dns = "exemple.org", challenge = "http-01"},
    { dns = "www.exemple.org", challenge = "tls-alpn-01"},
    { ip = "203.0.113.1", challenge = "http-01"},
]
hooks = ["http-01-echo", "tls-alpn-01-tacd-tcp", "git"]
```

Dans l'exemple ci-dessus, ACMEd envera la demande de signature du certificat au point d'entrée dont le nom est `Example CA` en utilisant le compte dont le nom est `Mon compte`. Ce certificat sera valide pour 3 identifiants : 2 noms de domaines et une adresse IP. Le nom de domaine `exemple.org` et l'adresse IP `203.0.113.1` seront validés à l'aide du défi http-01 alors que le nom de domaine `www.exemple.org` sera validé à l'aide du défi tls-alpn-01. Afin d'effectuer ces validations, les hooks `http-01-echo` et `tls-alpn-01-tacd-tcp` seront utilisés. Le hook `git` permet, lui, de suivre les fichiers du certificat (le certificat en lui-même et sa clé privée associée) à l'aide de git.


## Les variables d'environnement

Il est possible de définir des variables d'environnement dans le certificat ainsi que dans les identifiants :

``` toml
[[certificate]]
endpoint = "Example CA"
account = "Mon compte"
identifiers = [
    { dns = "exemple.org", challenge = "http-01"},
    { dns = "www.exemple.org", challenge = "tls-alpn-01", env.TACD_PORT="5010"},
    { ip = "203.0.113.1", challenge = "http-01", env.HTTP_ROOT = "/srv/http_bis"},
]
hooks = ["http-01-echo", "tls-alpn-01-tacd-tcp", "git"]
env.HTTP_ROOT = "/srv/http"
env.GIT_USERNAME = "Jane Doe"
env.GIT_EMAIL = "jane.doe@exemple.org"
```

Dans cet exemple, le répertoire racine du serveur web utilisé dans le hook `http-01-echo` sera `/srv/http` pour le nom de domaine `exemple.org` et `/srv/http_bis` pour l'adresse IP `203.0.113.1`. Vous noterez que, étant défini a un niveau plus précis, `/srv/http_bis` prends le dessus sur `/srv/http`.

De manière similaire, il est également possible de définir des variables d'environnement affectant le certificat au niveau du compte utilisateur ainsi qu'au niveau global. Les variables d'environnement définies dans un hook garderont une portée localisée au hook en question.


## Les attributs du sujet

Certaines autorités de certification peuvent permettre l'émission de certificats contenant des informations additionnelles sur l'entité qui en fait la demande. Ces attributs peuvent être définis grâce à la table `subject_attributes`. La liste des attributs possibles et leurs correspondance avec les différentes normes est disponible dans le manuel (`man 5 acmed.toml`).

``` toml
[[certificate]]
endpoint = "Example CA"
account = "Mon compte"
identifiers = [
    { dns = "exemple.org", challenge = "http-01"},
    { dns = "www.exemple.org", challenge = "tls-alpn-01"},
    { ip = "203.0.113.1", challenge = "http-01"},
]
hooks = ["http-01-echo", "tls-alpn-01-tacd-tcp", "git"]
subject_attributes.country_name = "FR"
subject_attributes.locality_name = "Paris"
subject_attributes.organization_name = "Example Corp."
```

Ces attributs étant signés par l'AC, cette dernière vous demandera très certainement de lui prouver la validité de ces informations par un moyen externe à ACME. Vous devrez donc, en tout logique, disposer d'un compte externe.


## Autres paramètres utiles

Sans chercher à détailler l'ensemble des paramètres possible qu'il est possible de définir pour un certificat, ceux qui semblent les plus utiles sont les suivants :

- `directory` : indique le répertoire dans lequel seront stockés le certificat et sa clé privée associée ;
- `file_name_format` : change le format utilisé pour définir le nom des fichiers du certificat et de sa clé privée associée ;
- `key_type` : le type de clé utilisé ;
- `kp_reuse` : si mis à `true`, la clé privée sera restera inchangée lors des renouvellements du certificat ;
- `name` : nom du certificat (utilisé pour le nom des fichiers associés).

Le détail des valeurs possibles, les valeurs par défaut ainsi que les autres paramètres moins fréquents sont disponibles dans le manuel (`man 5 acmed.toml`).
