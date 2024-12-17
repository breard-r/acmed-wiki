
[//]: # (Copyright 2019-2020 Rodolphe Bréard <rodolphe@breard.tf>)

[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)

ACMEd is a [daemon](https://en.wikipedia.org/wiki/Daemon_(computing)), which means it is designed to run in the background and executes all the required actions all by itself. Unlike some other ACME client, it therefore does not require any cron job or any other kind of timer.


## The configuration

When started, ACMEd reads its configuration, which is where all the instructions are defined. Those instructions are divided in the following categories :

- [Additional configuration files to include](Additional-configuration-files-to-include)
- Global options
- [Endpoints and rate limits](Endpoints.md)
- [Accounts](Accounts.md)
- [Certificates](Certificates.md)
- [Hooks and groups](Hooks-And-Groups.md)
- [Example acmed.toml config file](Example-DNS-Config.md)


## The files

By default, ACMEd configuration file is `/etc/acmed/acmed.toml`. Other files may be included. [Example acmed.toml file](Example-DNS-Config.md)

Currently there seems to still be an issue with wildcard dns challenges.

Account private keys and related data are stored in the `/etc/acmed/accounts/` directory. It is possible to define a different directory in the configuration. In any case, this directory should not be left accessible by any user, only ACMEd should access it (chmod 700).
In this directory, files names are the associated account's name in base64 (URL safe without padding) followed by `.account.bin`. As the extension states, those files contains binary data and are not meant to be used by a tool other than ACMEd.

Certificates and the associated private keys are stored in the `/etc/acmed/certs/` directory. It is possible to define a different directory in the configuration. The default file format is `<cert_name>_<cert_key_type>.<file_type>.pem` where:

- `cert_name` is the certificate's name.
- `cert_key_type` is the certificate's key type.
- `file_type` is either `crt` for the certificate itself or `pk` for the private key.
