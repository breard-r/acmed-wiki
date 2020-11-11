
[//]: # (Copyright 2019-2020 Rodolphe Bréard <rodolphe@breard.tf>)

[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)

ACMEd est un [daemon](https://fr.wikipedia.org/wiki/Daemon_(informatique)), ce qui signifie qu'il est prévu pour tourner en tache de fond et exécuter de lui-mêmes toutes les actions nécessaires. Contrairement à d'autres clients ACME, il ne nécessite donc aucune tâche cron ni aucun autre genre de minuteur.


## La configuration

À son lancement, ACMEd lit sa configuration où sont indiquées toutes les instructions nécessaires à son fonctionnement. Ces instructions sont réparties dans les catégories suivantes :

- [Fichiers de configuration à inclure](Fichiers-de-configuration-à-inclure)
- Options globales
- [Points d'entrée et limites de débit](Points-d'entrée-et-limites-de-débit)
- Comptes
- Certificats
- Hooks et groupes


## Les fichiers

Par défaut, le fichier de configuration d'ACMEd est `/etc/acmed/acmed.toml`. D'autres fichiers peuvent être inclus.

Les clés privées des comptes ainsi que les données associées sont stockées dans le répertoire `/etc/acmed/accounts/`. Il est possible de définir un répertoire alternatif grâce à une directive de configuration. Dans tous les cas, ce répertoire ne doit pas etre laissé en accès libre aux utilisateurs, seul ACMEd doit y avoir accès (chmod 700).
Dans ce répertoire, le nom des fichiers sont composés du nom du compte associé en base64 (mode protection d'URL sans remplissage) suivi par `.account.bin`. Comme le laisse supposer l'extension du fichier, ce dernier contient des données binaires et n'est donc pas prévu pour etre utilisé par un outil autre qu'ACMEd.

Les certificats et clés privées associées sont stockés dans le répertoire `/etc/acmed/certs/`. Il est possible de définir un répertoire alternatif grâce à une directive de configuration. Le format de fichier par défaut est `<cert_name>_<cert_key_type>.<file_type>.pem` où :

- `cert_name` est le nom du certificat.
- `cert_key_type` est le type de clé du certificat.
- `file_type` est soit `crt` pour le certificat lui-même soit `pk` pour la clé privée.
