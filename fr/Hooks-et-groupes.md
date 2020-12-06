
[//]: # (Copyright 2019-2020 Rodolphe Bréard <rodolphe@breard.tf>)

[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)

Afin de pouvoir s'adapter un maximum à votre environnement, ACMEd vous donne la possibilité de paramétrer les actions à mener lors de certaines circonstances. À cette fin, ACMEd permet de définir des commandes shell qui seront appelées lors de certaines phases de son exécution. Ces commandes shell pré-paramétrées sont ici dénommées des hooks.


## Les phases d'exécution

Il n'est possible de définir des hooks que pour les [comptes](Comptes) et les [certificats](Certificats). Lorsque, pour ces éléments, le processus atteint certaines phases d'exécutions, les hooks associés sont alors déclenchés. Notons que si plusieurs hooks ont été définis, ces derniers sont exécutés dans l'ordre dans lequel ils ont été déclarés.

### Phases des comptes

Dans le cadre des comptes, les seules phases d'exécution existantes sont celles relatives à la création et à la modification du fichier contenant les informations du compte en question (nom, clé privée, etc).

- `file-post-create` : se déclenche après la création d'un fichier
- `file-post-edit` : se déclenche après la modification d'un fichier
- `file-pre-create` : se déclenche avant la création d'un fichier
- `file-pre-edit` : se déclenche avant la modification d'un fichier

### Phases des certificats

Pour les certificats, les phases d'exécutions se répartissent en trois groupes.

Tout d'abord, les phases d'exécutions relatives à la réalisation des défis ACME.

- `challenge-dns-01` : se déclenche lorsqu'il est nécessaire de répondre à un défi DNS-01
- `challenge-dns-01-clean` : se déclenche après que le serveur distant a pu vérifier l'accomplissement d'un défi DNS-01
- `challenge-http-01` : se déclenche lorsqu'il est nécessaire de répondre à un défi HTTP-01
- `challenge-http-01-clean` : se déclenche après que le serveur distant a pu vérifier l'accomplissement d'un défi HTTP-01
- `challenge-tls-alpn-01` : se déclenche lorsqu'il est nécessaire de répondre à un défi TLS-ALPN-01
- `challenge-tls-alpn-01-clean` : se déclenche après que le serveur distant a pu vérifier l'accomplissement d'un défi TLS-ALPN-01

Ensuite, tout comme pour les comptes, il existe les phases d'exécutions relative à la création ou à la modification des fichiers de certificats et des clés privées associées.

- `file-post-create` : se déclenche après la création d'un fichier
- `file-post-edit` : se déclenche après la modification d'un fichier
- `file-pre-create` : se déclenche avant la création d'un fichier
- `file-pre-edit` : se déclenche avant la modification d'un fichier

Enfin, il existe une phase d'exécution de bilan.

- `post-operation` : se déclenche à la fin d'une demande de création ou de renouvellement de certificat


## Usage des hooks

ACMEd ne réalisant par lui-même aucune action consistant à répondre aux défis nécessaire à la validation des identifiants des certificats, il est impératif d'utiliser des hooks pour répondre à ces défis. Grace à ce système, ACMEd ne fait aucune pré-supposition sur la manière dont est composée votre infrastructure : c'est vous qui lui indiquez, grâce aux hooks, ce qu'il faut lancer comme commandes afin d'atteindre cet objectif.

Lorsqu'un certificat a été renouvelé, vous souhaiterez certainement recharger les différents services qui l'utilisent. Ceci pouvant se faire via différents systèmes d’initialisation (systemd, SysV init, Upstart, etc) ou par des moyens personnalisés, ACMEd ne fait encore une fois aucune pré-supposition sur votre infrastructure et vous laisse définir les commandes à lancer. Ce hook peut également être l'occasion d'envoyer une notification aux personnes administrant le système afin de leur rendre compte du déroulement de l'opération.

Les clés et certificats étant des éléments critiques, un grand nombre de personnes veulent les sauvegarder voir en archiver tout l'historique. Dans certaines infrastructures, il est nécessaire de déployer les certificats sur d'autres machines. C'est pour répondre à cette grande variété d'usages qu'ACMEd permet de définir des hooks relatifs à la création et la modification de certains fichiers.

Certains usages étant fortement répandus, ACMEd fourni un fichier de configuration, inclut par défaut, définissant des [hooks d'usage courant](Hooks-prédéfinis).


## Définition d'un hook

Pour définir un hook, il est nécessaire de lui donner un nom, les phases d'exécutions durant lesquelles sil doit se déclencher, la commande à lancer ainsi que les éventuels paramètres de cette dernière. Notons que les phases d'exécutions sont renseignées dans le tableau `type`.

```
[[hook]]
name = "example-hook-1"
type = ["post-operation"]
cmd = "echo"
args = [
    "-e",
    "Hello World!",
]
```

Ce hook exécutera donc la commande `echo -e "Hello World!"` à la fin d'une demande de création ou de renouvellement d'un certificat utilisant ce hook.

### Erreur d'exécution

Par défaut, lorsque la commande d'un hook se termine avec un code de retour indiquant une erreur, ACMEd considère que l'erreur impacte toute l'action en cours (renouvellement de certificat) et interrompra donc cette dernière. Dans les cas où un hook doit pouvoir échouer sans impacter l'ensemble de l'action, il est possible d'indiquer `allow_failure = true`.

### Entrées et sorties

Les programmes disposent d'une entrée standard, d'une sortie standard et d'une sortie d'erreur. Les commandes des hooks n'y font pas exception et il donc est possible d'indiquer, à l'aide des paramètres `stdin`, `stdout` et `stderr`, le chemin vers un fichier sur lequel sera branché l'entrée ou la sortie. Pour l'entrée standard, il est également possible de directement indiquer une chaîne de caractère qui sera écrite dessus à l'aide de `stdin_str`. Bien entendu, `stdin` et `stdin_str` sont mutuellement exclusifs.

```
[[hook]]
name = "example-hook-2"
type = ["post-operation"]
allow_failure = true
cmd = "sed"
args = [
    "-e",
    "s/Ceci/Voici/",
    "-e",
    "s/est/une/",
]
stdin_str = """Ceci est
une chaîne de caractères
écrite sur plusieurs
lignes.
"""
stdout = "/tmp/example-hook.txt"
```

Le hook ci-dessus lit en entrée une chaîne de caractères, exécute la commande `cat -e` et écrit le contenu de la sortie standard dans le fichier `/tmp/example-hook.txt`. Après exécution du hook, ce fichier contiendra donc le contenu suivant :

```
Voici une
une chaîne de caractères
écrite sur plusieurs
lignes.

```

### Les groupes

Dans certains cas, il est nécessaire d'exécuter plusieurs commandes pour atteindre un unique objectif. Chacun de ces commandes étant un hook à part, il peut être fastidieux de définir l'ensemble de ces commandes dans chaque compte ou certificat les utilisant. Afin d'éviter ça, il est possible de regrouper plusieurs hooks en un seul en définissant un groupe.

Un groupe se définit avec un nom et la liste des hooks qu'il regroupe. Comme d'usage, les hooks seront exécutés dans l'ordre dans lesquels ils ont été définis.

```
[[group]]
name = "example-group"
hooks = ["example-hook-1", "example-hook-2"]
```

Les groupes sont considérés comme des hooks. À ce titre, il est possible d'indiquer le nom d'un groupe à la place du nom d'un hook dans un compte, un certificat ou même un autre groupe. Si vous regardez le contenu du fichier définissant les hooks prédéfinis précédemment évoqués, vous remarquerez que ces hooks sont en réalité des groupes regroupant plusieurs hooks.


## L'environnement d'exécution

S'il est très utile de pouvoir lancer des commandes prédéfinies, ceci s'avère totalement insuffisant. En effet, s'il n'est pas possible de récupérer et utiliser des informations détaillées sur le contexte dans lequel la commande doit s'exécuter, cela ne sert a rien. Par exemple, pour résoudre un défi ACME, il est impératif de savoir de quel identifiant il s'agit, la preuve et ainsi de suite.

La solution qui a été retenue est d'utiliser un moteur de templates qui sera utilisé sur différents éléments avant que la commande ne soit exécutée. Le moteur de template actuellement utilisé est une ré-implémentation de [handlebars](https://handlebarsjs.com/). Les éléments qui seront interprétés par ce moteur de templates sont les paramètres de la commandes (`args`) ainsi que les entrées/sorties (`stdin`, `stdin_str`, `stdout`, et `stderr`). La commande elle-même (`cmd`) n'est pas considérée comme un template.

En fonction de la phase d'exécution, différentes variables sont accessibles depuis les templates.

### `challenge-dns-01` et `challenge-dns-01-clean`

- `challenge` : chaîne de caractère indiquant le type de défi (`dns-01`)
- `env` : tableau associatif de chaînes de caractères contenant les variables d'environnement
- `identifier` : chaîne de caractères représentant le nom de l'identifiant concerné
- `is_clean_hook` : booléen mis à faux s'il s'agit de `challenge-dns-01` et à vrai s'il s'agit de `challenge-dns-01-clean`
- `proof` : contenu de la preuve devant être écrite dans le champ TXT de la zone DNS pour le sous-domaine `_acme-challenge`

### `challenge-http-01` et `challenge-http-01-clean`

- `challenge` : chaîne de caractère indiquant le type de défi (`http-01`)
- `env` : tableau associatif de chaînes de caractères contenant les variables d'environnement
- `file_name` : nom du fichier devant contenir la preuve (n'inclue pas `.well-known/acme-challenge/`)
- `identifier` : chaîne de caractères représentant le nom de l'identifiant concerné
- `is_clean_hook` : booléen mis à faux s'il s'agit de `challenge-http-01` et à vrai s'il s'agit de `challenge-http-01-clean`
- `proof` : contenu de la preuve devant être écrite dans `file_name`

### `challenge-tls-alpn-01` et `challenge-tls-alpn-01-clean`

- `challenge` : chaîne de caractère indiquant le type de défi (`tls-alpn-01`)
- `env` : tableau associatif de chaînes de caractères contenant les variables d'environnement
- `identifier` : chaîne de caractères représentant le nom de l'identifiant concerné
- `identifier_tls_alpn` : chaîne de caractères représentant le nom de l'identifiant concerné sous une forme adaptée au défi TLS-ALPN
- `is_clean_hook` : booléen mis à faux s'il s'agit de `challenge-tls-alpn-01` et à vrai s'il s'agit de `challenge-tls-alpn-01-clean`
- `proof` : chaîne de caractères représentant le contenu de l'extension `acmeIdentifier` devant être utilisée dans le certificat auto-signé

### `file-post-create`, `file-post-edit`, `file-pre-create` et `file-pre-edit`

- `env` : tableau associatif de chaînes de caractères contenant les variables d'environnement
- `file_directory` : chemin du dossier où se trouve le fichier concerné
- `file_name` : nom du fichier concerné
- `file_path` : chemin complet du fichier concerné

### `post-operation`

- `env` : tableau associatif de chaînes de caractères contenant les variables d'environnement
- `identifiers` : tableau de chaînes de caractères représentant le nom des identifiant du certificat
- `is_success` : booléen indiquant si l'opération s'est déroulée correctement ou non
- `key_type` : chaîne de caractères représentant le type de clé asymétrique utilisée
- `status` : description humainement lisible de l'état de l'opération

### Exemple

```
[[hook]]
name = "http-01-example"
type = ["challenge-http-01"]
cmd = "echo"
args = ["{{proof}}"]
stdout = "{{#if env.HTTP_ROOT}}{{env.HTTP_ROOT}}{{else}}/var/www{{/if}}/{{identifier}}/.well-known/acme-challenge/{{file_name}}"
```

Ce hook utilise la commande `echo` pour, lors d'un défi http-01, écrire la preuve dans un fichier qui sera servi par le serveur web. La preuve est donc passée en paramètre de la commande et la sortie standard est redirigée vers le fichier dans lequel l'écrire.

Le chemin de ce fichier de sortie est particulièrement intéressant. Tout d'abord, la partie `{{#if env.HTTP_ROOT}}{{env.HTTP_ROOT}}{{else}}/var/www{{/if}}/{{identifier}}` permet, si la variable d'environnement `HTTP_ROOT` est définie, de l'utiliser comme racine du serveur web et, dans le cas contraire, d'utiliser `/var/www`. Ensuite, la partie `/{{identifier}}` impose d'avoir un sous dossier contenant le nom de l'identifiant d'où le serveur web sert les fichiers relatifs à ce nom de domaine. Enfin, la partie `/.well-known/acme-challenge/{{file_name}}` concerne le le chemin qui sera utilisé dans l'URL.


## Erreurs communes

Des hooks mal définis sont la première cause d'erreur dans ACMEd. L'être humain étant ce qu'il est, la plupart des erreurs devraient avoir pour cause l'une des raisons suivantes.

- Droits d'accès : Les commandes sont exécutées par ACMEd et donc par l'utilisateur qui l'a lui-même lancé. Attention à l'utilisateur, au groupe, aux droits et aux éventuelles ACL.
- Création des dossiers : Créer un fichier dans un dossier qui n'existe pas génère une erreur. Pensez à faire un `mkdir -p` si vous vous attendez à ce qu'un dossier existe.
- Mauvaise interface avec le serveur web : Si l'on y fait pas attention, il est aisé, par exemple pour le défi HTTP-01, d'avoir une différence entre le chemin où ACMEd écrit la preuves et le chemin qui sera servi par le serveur web.
- Collision : ACMEd pouvant exécuter plusieurs défis simultanément, y compris pour un même identifiant, il convient de s'assurer que le comportement des hooks ne peuvent pas affecter les autres hooks. Ceci est particulièrement vrai dans les hooks de « nettoyage » dont le rôle est d'enlever les preuves une fois le défi réalisé.
