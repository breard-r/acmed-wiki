
[//]: # (Copyright 2019-2020 Rodolphe Bréard <rodolphe@breard.tf>)

[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)

A configuration file can include other files using the `include` directive. This directive is an array of strings representing the relative or absolute path to one or more file to include. If relative, it is relative to the configuration file which included it.

Unix style globing is supported.

``` toml
include = [
    "/etc/acmed/endpoints.toml",
    "certs-enabled/*.toml"
]
```

The above example includes one or more files. The `/etc/acmed/endpoints.toml` file is included first, then every file located in the `certs-enabled` sub-directory with a `.toml` extension.
