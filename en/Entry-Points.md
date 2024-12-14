[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)

## Entry points

In order to obtain a certificate, you must have it signed by a certification authority, which is automated by the ACME protocol. Since this protocol is standard, it can be used with many different certification authorities. It is therefore necessary to indicate in the ACMEd configuration which certification authorities you wish to use. It is of course possible to use several certification authorities, each of which must sign one or more certificates.

Certification authorities provide a URL pointing to their ACME server. In the ACMEd configuration, we must therefore create what we will call an entry point (endpoint in English) which will group, at a minimum, a name of your choice and the URL of the ACME server.

``` toml
[[endpoint]]
name = "Example CA"
url = "https://acme.example.org/dir"
```

For information purposes, a list of the main certification authorities supporting the ACME protocol has been created.
Terms of Service

Certification authorities define terms of service that must be accepted before using their services. To indicate that you have read and accepted these T&Cs, you must set the tos_agreed configuration directive to true for the corresponding entry point.

``` toml
[[endpoint]]
name = "Example CA"
url = "https://acme.example.org/dir"
tos_agreed = true
```

### Rate Limits

In order to obtain a certificate, ACMEd makes a certain number of requests to the ACME server of the certification authority. In order to avoid overloading its servers, this certification authority can refuse to respond to too many requests that would occur in a given time interval. In order not to harm the certification authorities and to avoid running into these potential limits, it is possible to configure ACMEd so that it limits the rate of requests it sends to a given entry point by itself. This is done by defining a rate-limit section that consists of:

| Identifier | Description |
| --: | :-- |
|name :| a name of your choice|
|number :| a maximum number of requests|
|period :| the period of time during which the number of requests previously defined must not be exceeded|

In the following example, the rate limit named my limit 1 will prevent the sending of more than 2 requests per second.

``` toml
[[rate-limit]]
name = "my1s"
number = 2
period = "1s"
```

### The time period

A time limit is a string whose format is composed of a series of number + unit pairs that are added to each other. Possible units are:

- `s`: second
- `m`: minute
- `h`: hour
- `d`: day
- `w`: week

The order is not important and it is possible to specify the same unit multiple times. Thus, 1d42s and 40s20h4h2s both represent a duration of 1 day and 42 seconds.
Using rate limits

Of course, you must add, in the configuration of an entry point, the rate limits that it must respect. This is done using the rate_limits directive. By taking our previous examples, we therefore obtain:

``` toml
[[rate-limit]]
name = "my1s"
number = 2
period = "1s"

[[endpoint]]
name = "Example CA"
url = "https://acme.example.org/dir"
tos_agreed = true
rate_limits = ["my1s"]
```

It is of course possible to specify several rate limitations for the same entry point.

``` toml
[[rate-limit]]
name = "my1s"
number = 2
period = "1s"

[[rate-limit]]
name = "my2s"
number = 500
period = "1h"

[[endpoint]]
name = "Example CA"
url = "https://acme.example.org/dir"
tos_agreed = true
rate_limits = ["my1s", "my2s"]
```

In the previous example, no more than 2 requests per second will be sent within the limit of 500 requests per hour.






