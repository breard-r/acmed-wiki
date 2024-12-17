[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)

## Endpoints

In order to obtain a certificate, you must have it signed by a certification authority, which is automated by the ACME protocol. Since this protocol is standard, it can be used with many different certification authorities. It is therefore necessary to indicate in the ACMEd configuration which certification authority you wish to use. It is of course possible to use several certification authorities simultaneously as needed., each of which must sign one or more certificates.

Certification authorities provide a URL pointing to their ACME server. In the ACMEd configuration, we must therefore create an endpoint which identifies and associates a certification authority with it's name, url, and optionally other details like the file name format, rate limits, renew delay, etc.

``` toml
[[endpoint]]
name = "Example CA"
url = "https://acme.cert-authority.org/dir"
```

For information purposes, a list of the main certification authorities supporting the ACME protocol has been created.

---

##Terms of Service

Certification authorities usually require that their terms of service be accepted before using their services. To indicate that you have read and accepted these terms and conditions, you must set the `tos_agreed` configuration directive to true for the corresponding endpoint.

``` toml
[[endpoint]]
name = "Example CA"
url = "https://acme.cert-authority.org/dir"
tos_agreed = false
```

### Rate Limits

In order to obtain a certificate, ACMEd makes a certain number of requests to a [certificate authority's](Additional-Certificate-Authorities.md) ACME server. In order to avoid overloading its servers, certification authorities can refuse to respond when too many requests are initiated simultaneously. In order not to harm the certification authorities and to avoid running into these potential limits, it is possible to configure ACMEd so that it limits the rate of requests it sends to a given endpoint. This is done by defining a rate-limit section that consists of:

| Identifier | Description |
| --: | :-- |
|name :| a name of your choice|
|number :| a maximum number of requests|
|period :| the period of time during which the number of requests previously defined must not be exceeded|

In the following example, the rate limit named my limit 1 will prevent the sending of more than 2 requests per second.

``` toml
[[rate-limit]]
name = "mytwiceasec"
number = 2
period = "1s"
```

### The time period

A time limit is a string whose format is composed of a series of number + unit pairs that are added to each other. Time unit codes are:

- `s`: second
- `m`: minute
- `h`: hour
- `d`: day
- `w`: week

The order is not important and it is possible to specify the same unit multiple times. Thus, 1d42s and 40s20h4h2s both represent a duration of 1 day and 42 seconds.

###Using rate limits

Of course, you must add, in the configuration of an entry point, the rate limits that it must respect. This is done using the `rate_limit` directive. By taking our previous examples, we therefore obtain:

``` toml
[[rate-limit]]
name = "mytwiceasec"
number = 2
period = "1s"

[[endpoint]]
name = "Example CA"
url = "https://acme.example.org/dir"
tos_agreed = true
rate_limits = ["mytwiceasec"]
```

It is of course possible to specify several rate limitations for the same entry point.

``` toml
[[rate-limit]]
name = "my1s"
number = 2
period = "1s"

[[rate-limit]]
name = "my500perhour"
number = 500
period = "1h"

[[endpoint]]
name = "Example CA"
url = "https://acme.example.org/dir"
tos_agreed = true
rate_limits = ["mytwiceasec", "my500perhour"]
```

In the previous example, no more than 2 requests per second will be sent within the limit of 500 requests per hour.

