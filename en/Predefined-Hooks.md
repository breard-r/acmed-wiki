[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)

Some [hooks](Hooks-And-Groups.md) are so common, ACMEd has implemented for you.

By default, ACMEd includes the following hooks which handle the most common cases.

| Name | Execution phases |
| --: | :-- |
| http-01-echo | `challenge-http-01`, `http-01-echo-clean` |
| tls-alpn-01-tacd-tcp |	`challenge-tls-alpn-01`, `challenge-tls-alpn-01-clean` |
| tls-alpn-01-tacd-unix | 	`challenge-tls-alpn-01`, `challenge-tls-alpn-01-clean` |
| git |	`file-pre-create`, `file-pre-edit`, `file-post-create`, `file-post-edit` |

These hooks primarily use standard shell commands. While careful attention has been paid to portability across operating systems, some exotic systems may not support certain commands or options. If a hook does not work properly, look at its definition and compare the commands and options used with your system's documentation.

---

## `http-01-echo`

This hook group contains the hooks that allow the resolution of the `http-01` challenge by writing the verification `proof` into the file to be served by a web server. This file is written into a folder with the path `<web_root>/<identifer>/.well-known/acme-challenge/` where:

- `<web_root>` is the root directory of the web server, by default `/var/www/`
- `<identifier>` is the identifier to validate, so the domain name or IP address.

Be careful not to confuse the identifier to validate and the name of the certificate. In a certificate, you must solve a challenge by identifier and only the name of the latter counts: the name of the certificate is not needed.

It is possible to change the root directory of the web server by setting the environment variable `HTTP_ROOT`

---

## `tls-alpn-01-tacd-tcp`

This group contains the hooks that allow the resolution of the `tls-alpn-01` challenge using tacd. For each validation, a tacd server is therefore launched and then stopped once this validation is done. The tacd configuration is accomplished using the following environment variables:

- TACD_PID_ROOT: Folder where the PID of the executable will be written. By default, `/run` is used.
- TACD_HOST: Host or address on which the server should listen. The default value is the identifier to be validated.
- TACD_PORT: The port on which the server should listen. Default is 5001.

Note that the PID file name is of the form `tacd_<identifiant>.pid`. If you have different certificates that contain common identifiers and these identifiers are validated with tacd, then you should specify `TACD_PID_ROOT` different values ​​for each of these certificates (example: `/run/cert_1/` and `/run/cert_2/`) to avoid two instances of tacd being launched with the same PID file.

---

## `tls-alpn-01-tacd-unix`

Same as `tls-alpn-01-tacd-tcp` except that tacd no longer listens on a TCP interface but on a UNIX socket. The environment variables `TACD_HOST` and `TACD_PORT` are then replaced by `TACD_SOCK_ROOT` whose value determines the folder in which the UNIX socket will be created. The default value is `/run`.

The UNIX socket name is `tacd_<identifiant>.sock`, which requires the same precautions to be taken as with the PID file name.

---

## git

This group contains hooks to use git to track created files. If the git repository does not exist, it is automatically created. Any files created or modified by ACMEd are added and a commit is then made. Files resulting from hooks will not trigger this hook.

It is possible to customize commits using the following environment variables:

- GIT_USERNAME: specifies the value of the git parameter `user.name`
- GIT_EMAIL: specifies the value of the git parameter `user.email`

