
[//]: # (Copyright 2019-2020 Rodolphe Bréard <rodolphe@breard.tf>)

[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)


## Les points d'entrée

Afin d'obtenir un certificat, vous devez le faire signer par une autorité de certification, ce qui est automatisé par le protocole ACME. Ce protocole étant standard, il peut être utilisé avec de nombreuses autorités de certification différentes. Il est donc nécessaire d'indiquer dans la configuration d'ACMEd quelles autorités de certifications vous souhaitez utiliser. Il est bien entendu possible d'utiliser plusieurs autorités de certifications, chacune d'entre elles devant signer un ou plusieurs certificats.

Les autorités de certification fournissent une URL pointant sur leur serveur ACME. Dans la configuration d'ACMEd, nous devons donc créer ce que l'on appellera un point d'entrée (endpoint en anglais) qui regroupera, à minima, un nom de votre choix et l'URL du serveur ACME.

``` toml
[[endpoint]]
name = "Example CA"
url = "https://acme.example.org/dir"
```

### Les conditions générales d'utilisation

Les autorités de certification définissent des conditions générales d'utilisation (« terms of service » en anglais) qu'il convient d'accepter avant d'utiliser leurs services. Afin d'indiquer que vous avez lu et accepté ces CGU vous devez, pour le point d'entrée correspondant, définir la directive de configuration `tos_agreed` à la valeur `true`.

``` toml
[[endpoint]]
name = "Example CA"
url = "https://acme.example.org/dir"
tos_agreed = true
```


## Les limites de débit

Afin d'obtenir un certificat, ACMEd effectue un certain nombres de requêtes auprès du serveur ACME de l'autorité de certification. Afin que ses serveurs ne soient pas surchargés, cette autorité de certification peut refuser de répondre à un trop grand nombre de requêtes qui auraient lieux dans un intervalle de temps donné. Afin de ne pas nuire aux autorités de certification et d'éviter de se heurter à ces éventuelles limites, il est possible de configurer ACMEd de manière à ce qu'il limite de lui-même le débit des requêtes qu'il envoie à un point d'entrée donné. Ceci se fait en définissant une section `rate-limit` qui se compose de :

- `name` : un nom de votre choix
- `number` : un nombre maximale de requêtes
- `period` : la période de temps durant laquelle le nombre de requêtes précédemment défini ne doit pas être dépassé

Dans l'exemple suivant, la limite de débit nommée `ma limite 1` empêchera l'émission de plus de 2 requêtes par secondes.

``` toml
[[rate-limit]]
name = "ma limite 1"
number = 2
period = "1s"
```

### La période de temps

Une limite de temps est une chaîne de caractère dont le format est composé d'une série de couples nombre + unité qui sont additionnés les uns aux autres. Les unités possibles sont les suivantes :

- `s` : seconde
- `m` : minute
- `h` : heure
- `d` : jour
- `w` : semaine

L'ordre n'est pas important et il est possible de spécifier plusieurs fois la même unité. Ainsi, `1d42s` et `40s20h4h2s` représentent toutes les deux une durée de 1 jour et 42 secondes.

### Utiliser les limites de débit

Bien entendu, il est faut ajouter, dans la configuration d'un point d'entrée, les limites de débit qu'il se doit de respecter. Ceci se fait à l'aide de la directive `rate_limits`. En reprenant nos exemples précédent, nous obtenons donc :

``` toml
[[rate-limit]]
name = "ma limite 1"
number = 2
period = "1s"

[[endpoint]]
name = "Example CA"
url = "https://acme.example.org/dir"
tos_agreed = true
rate_limits = ["ma limite 1"]
```

Il est bien entendu possible de spécifier plusieurs limitations de débit pour un même point d'entrée.

``` toml
[[rate-limit]]
name = "ma limite 1"
number = 2
period = "1s"

[[rate-limit]]
name = "ma limite 2"
number = 500
period = "1h"

[[endpoint]]
name = "Example CA"
url = "https://acme.example.org/dir"
tos_agreed = true
rate_limits = ["ma limite 1", "ma limite 2"]
```

Dans l'exemple précédent, il ne sera pas envoyé plus de 2 requêtes par seconde dans la limites de 500 requêtes par heure.
