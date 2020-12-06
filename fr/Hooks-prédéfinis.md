
[//]: # (Copyright 2019-2020 Rodolphe Bréard <rodolphe@breard.tf>)

[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)


De base, ACMEd inclue un fichier contenant des [hooks](Hooks-et-groupes) répondant aux cas les plus généraux.

| Nom                   | Phases d'exécution                                                       |
| --------------------- | ------------------------------------------------------------------------ |
| http-01-echo          | `challenge-http-01`, `http-01-echo-clean`,                               |
| tls-alpn-01-tacd-tcp  | `challenge-tls-alpn-01`, `challenge-tls-alpn-01-clean`                   |
| tls-alpn-01-tacd-unix | `challenge-tls-alpn-01`, `challenge-tls-alpn-01-clean`                   |
| git                   | `file-pre-create`, `file-pre-edit`, `file-post-create`, `file-post-edit` |

Ces hooks utilisent principalement des commandes shell standard. Bien qu'il ait été porté une attention particulière à la portabilité entre les systèmes d'exploitation, certains système exotiques peuvent cependant ne pas supporter certaines commandes ou options. Si un hook ne fonctionne pas correctement, regardez sa définition et comparer les commandes et options utilisées avec la documentation de votre système.


## http-01-echo

Ce groupe contient les hooks permettant la résolution du défi http-01 en écrivant la valeur de vérification dans le fichier destiné à être servi par un serveur web. Ce fichier est écrit dans un dossier dont le chemin est `<racine_du_serveur_web>/<identifiant>/.well-known/acme-challenge/` où :

- `<racine_du_serveur_web>` est le répertoire racine du serveur web, par défaut `/var/www/` ;
- `<identifiant>` est l'identifiant à valider, donc le nom de domaine, l'adresse IP, etc.

Attention à ne pas confondre l'identifiant à valider et le nom du certificat. Dans un certificat, il faut résoudre un défi par identifiant et seul le nom de ce dernier compte : le nom du certificat n'est pas utilisé dans ce hook.

Il est possible de modifier le répertoire racine du serveur web en définissant la variable d'environnement `HTTP_ROOT`.


## tls-alpn-01-tacd-tcp

Ce groupe contient les hooks permettant la résolution du défi tls-alpn-01 en utilisant tacd. Pour chaque validation, un serveur tacd est donc lancé puis est arrêté une fois cette validation effectuée. Le paramétrage de tacd s'effectue à l'aide des variables d'environnement suivantes :

- `TACD_PID_ROOT` : Dossier dans lequel sera inscrit le PID de l'exécutable. Par défaut, `/run` est utilisé.
- `TACD_HOST` : Hôte ou adresse sur laquelle le serveur doit écouter. La valeur par défaut est celle de l'identifiant à valider.
- `TACD_PORT` : Le port sur lequel le serveur doit écouter. Par défaut, 5001.

Il est à noter que le nom du fichier de PID est de la forme `tacd_<identifiant>.pid`. Si vous avez des certificats différents contenant des identifiants en commun et que ces identifiants sont validés avec tacd, vous devriez alors spécifier des valeurs de `TACD_PID_ROOT` différentes pour chacun de ces certificats (exemple: `/run/cert_1/` et `/run/cert_2/`) afin d'éviter que deux instances de tacd ne soient lancées avec le même fichier de PID.


## tls-alpn-01-tacd-unix

Identique à `tls-alpn-01-tacd-tcp` à la différence près que tacd n'écoute plus sur une interface TCP mais une [socket UNIX](https://fr.wikipedia.org/wiki/Berkeley_sockets#Socket_UNIX). Les variables d'environnement `TACD_HOST` et `TACD_PORT` sont alors remplacées par `TACD_SOCK_ROOT` dont la valeur détermine le dossier dans lequel la socket UNIX sera crée. La valeur par défaut est `/run`.

Le nom de la socket UNIX est `tacd_<identifiant>.sock`, ce qui impose d'utiliser les mêmes précautions qu'avec le nom du fichier de PID.


## git

Ce groupe contient les hooks permettant d'utiliser git pour suivre les fichiers créés. Si le dépôt git n'existe pas, il est automatiquement créé. Tout fichier créé ou modifié par ACMEd est ajouté et un commit est ensuite réalisé. Les fichiers résultants de hooks ne déclencheront pas ce hook.

Il est possible de personnaliser les commit à l'aide des variables d'environnement suivantes :

- `GIT_USERNAME` : spécifie la valeur du paramètre git `user.name` ;
- `GIT_EMAIL` : spécifie la valeur du paramètre git `user.email`.
