[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)

A configuration file can include other files using the include directive. This directive is an array of strings representing the relative or absolute path to one or more files to include. If the path is relative, then it is relative to the file that included it.

It is possible to use the Unix shell expansion syntax.


``` toml
include = [
    "/etc/acmed/endpoints.toml",
    "certs-enabled/*.toml"
]
```


The example above includes several files. First the file /etc/acmed/endpoints.toml, then all the files located in the subfolder certs-enabled and whose extension is .toml.

