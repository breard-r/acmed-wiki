[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)

# Certificates

In order to obtain a certificate signed by a certification authority, you need to define what you want. To do this, you need to indicate, at a minimum:

- The [endpoint](endpoint) to use;
- The [user account](user-account) to use on this entry point;
- The [identifiers](identifiers) to validate (domain names, IP addresses, etc.) as well as the challenge to use;
- The [hooks](hooks) used to respond to the challenges.

## Endpoints:

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

In the example above, ACMEd will send the certificate signing request to the endpoint named `Example CA` using the account named `My Account`. This certificate will be valid for 3 identifiers: 2 domain names and an IP address. The domain name `example.org` and the IP address `203.0.113.1` will be validated using the http-01 challenge while the domain name `www.example.org` will be validated using the `tls-alpn-01` challenge. In order to perform these validations, the `http-01-echo` and `tls-alpn-01-tacd-tcp` hooks will be used. The git hook allows you to track the certificate files (the certificate itself and its associated private key) using git.

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
In this example, the root directory of the web server is used in the `http-01-echo` hook will be hosted at `/srv/http` for the domain name `example.org` and `/srv/http_bis` for the IP address `203.0.113.1`. The HTTP_ROOT defined in the ip identifier will take precedence over the more general env.HTTP_ROOT definition.

Note that it is also possible to define environment variables affecting a certificate at both the global level, and the account level. Environment variables defined in a hook will only apply to that specific hook.

## Subject Attributes

Some certificate authorities allow additional information to be embedded in the certificate.

These fields are defined using the `subject_attributes` identifier as in the following example. The available fields are detailed in the man 5 acmed.toml pages.

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
subject_attributes.country_name = "FR"
subject_attributes.locality_name = "Paris"
subject_attributes.organization_name = "Example Corp."

```
Certificate Authorities that embed this information should take steps to confirm the information, and therefore should be among the Certificate Authorities that require pre-registration.


## Useful parameters

The following parameters are considered to be the most useful:

| | Summary: |
| --: | :-- |
| `directory`:| indicates the directory in which the certificate and its associated private key will be stored;|
| `file_name_format`:| changes the format used to define the name of the certificate files and its associated private key;|
|`key_type`:| the type of key used;|
|`kp_reuse`:| if set to true, the private key will remain unchanged when the certificate is renewed;|
|`name`:| name of the certificate (used for the name of the associated files).|

Complete details of the fields, default values ​​as well as the other less frequent parameters are available in the manual (`man 5 acmed.toml`).

