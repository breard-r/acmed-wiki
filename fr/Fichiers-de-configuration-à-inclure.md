
[//]: # (Copyright 2019-2020 Rodolphe Bréard <rodolphe@breard.tf>)

[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)

Un fichier de configuration peut en inclure d'autres à l'aide de la directive `include`. Cette directive est un tableau de chaînes de caractères représentant le chemin relatif ou absolu vers un ou plusieurs fichiers à inclure. Si le chemin est relatif, il est alors relatif par rapport au fichier qui l'a inclut.

Il est possible d'utiliser la syntaxe d'expansion des shell Unix.

``` toml
include = [
    "/etc/acmed/endpoints.toml",
    "certs-enabled/*.toml"
]
```

L'exemple ci-dessus inclue plusieurs fichiers. Tout d'abord le fichier `/etc/acmed/endpoints.toml`, puis ensuite tous les fichiers situés dans le sous-dossier `certs-enabled` et dont l'extension est `.toml`.
