[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)


In order to obtain a certificate signed by a certification authority, you need to define what you want. To do this, you need to indicate, at a minimum:

- The [endpoint](endpoint) to use;
- The [user account](user-account) to use on this entry point;
- The [identifiers](identifiers) to validate (domain names, IP addresses, etc.) as well as the challenge to use;
- The [hooks](hooks) used to respond to the challenges.

---

## Endpoint:

``` toml
[[certificate]]
endpoint = "Example CA"
account = "My Account"
identifiers = [
    { dns = "exemple.org", challenge = "http-01"},
    { dns = "www.exemple.org", challenge = "tls-alpn-01"},
    { ip = "203.0.113.1", challenge = "http-01"},
]
hooks = ["http-01-echo", "tls-alpn-01-tacd-tcp", "git"]
```

In the example above, ACMEd will send the certificate signing request to the endpoint named Example CA using the account named My Account. This certificate will be valid for 3 identifiers: 2 domain names and an IP address. The domain name example.org and the IP address 203.0.113.1 will be validated using the http-01 challenge while the domain name www.example.org will be validated using the tls-alpn-01 challenge. In order to perform these validations, the http-01-echo and tls-alpn-01-tacd-tcp hooks will be used. The git hook allows you to track the certificate files (the certificate itself and its associated private key) using git.

---

## Environment variables

It is possible to define environment variables in the certificate as well as in the identifiers:

``` toml
[[certificate]]
endpoint = "Example CA"
account = "My Account"
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
In this example, the root directory of the web server used in the http-01-echo hook will be /srv/http for the domain name example.org and /srv/http_bis for the IP address 203.0.113.1. You will notice that, being defined at a more precise level, /srv/http_bis takes precedence over /srv/http.

Similarly, it is also possible to define environment variables affecting the certificate at the user account level as well as at the global level. Environment variables defined in a hook will keep a scope localized to the hook in question.

---

## Subject Attributes

Some certificate authorities may allow the issuance of certificates containing additional information about the entity requesting them. These attributes can be defined using the subject_attributes table. The list of possible attributes and their correspondence with the different standards is available in the manual (man 5 acmed.toml).

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
Since these attributes are signed by the CA, the latter will most likely ask you to prove the validity of this information by a means external to ACME. You will therefore logically need to have an external account.

---

## Other useful parameters

Without trying to detail all the possible parameters that can be defined for a certificate, those that seem the most useful are the following:

| | Summary: |
| --: | :-- |
| directory:| indicates the directory in which the certificate and its associated private key will be stored;|
| file_name_format:| changes the format used to define the name of the certificate files and its associated private key;|
|key_type:| the type of key used;|
|kp_reuse:| if set to true, the private key will remain unchanged when the certificate is renewed;|
|name:| name of the certificate (used for the name of the associated files).|

The details of the possible values, the default values ​​as well as the other less frequent parameters are available in the manual (man 5 acmed.toml).

