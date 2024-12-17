[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)

As computing environments vary widely, it is necessary for ACMEd to be adaptable. That is accomplished with ACMEd's ability to run shell commands or call custom scripts for any required certificate processing. In the world of ACMEd, these custom commands are called `hooks`.

---

## Phases of Execution

Hooks can only be defined for [accounts](Accounts.md) and [certificates](Certificates.md). When, for these elements, the process reaches certain stages of execution, the associated hooks are triggered. Note that groups of hooks are executed in the order in which they were declared.

### Accounting Phases

In the context of accounts, the only existing execution phases are those relating to the creation and modification of the file containing the information of the account in question (name, private key, etc.).

Before/After Creation:
- `file-pre-create`: fires before a file is created
- `file-post-create`: Triggered after a file is created

Before/After Modification:
- `file-pre-edit`: fires before a file is modified
- `file-post-edit`: Triggered after a file is modified

---

## Certificate Phases

Certificates are broken down into the following groups:

Before/After a DNS Challenge:
- `challenge-dns-01`: Triggered when a DNS-01 challenge is required
- `challenge-dns-01-clean`: fires after the remote server has successfully verified the completion of a DNS-01 challenge

Before/After a HTTP Challenge:
- `challenge-http-01`: Fires when a HTTP-01 challenge is required
- `challenge-http-01-clean`: Fires after the remote server has successfully verified the completion of an HTTP-01 challenge

Before/After a TLS Challenge:
- `challenge-tls-alpn-01`: Triggered when a TLS-ALPN-01 challenge is required
- `challenge-tls-alpn-01-clean`: fires after the remote server has successfully verified the completion of a TLS-ALPN-01 challenge

Before/After Certificate Creation:
- `file-pre-create`: fires before a certificate is created
- `file-post-create`: Triggered after a certificate is created

Before/After Certificate Modification:
- `file-pre-edit`: fires before a certificate is modified
- `file-post-edit`: Triggered after a certificate is modified

And the End phase:
- `post-operation`: Triggered at the end of a certificate creation or renewal request

---

## Using hooks

Since ACMEd does not perform any action itself to pass a challenge, it is necessary that hooks perform all necessary actions to validate a certificate challenge. ACMEd's design allows for very flexible and customized actions to be taken at each step in the process.

For example, after a certificate has been granted, it is usually necessary to restart the various services that utilize the certificate. ACMEd allow that to be done easily regardless of which init system is utilized: systemd, SysV init, SMF, Upstart, etc.

Below, there is even an example of sending an e-mail using sendmail when a certificate renewal is attempted.

At a minimum, a hook definition requires a name, execution phase, and command to execute.

Example 'echo -e "Hello World!"' hook:

```
[[hook]]
name = "example-hook-1"
type = ["post-operation"]
cmd = "echo"
args = [
    "-e",
    "Hello World!",
]
```

### Runtime error

By default, ACMEd considers any error in a hook to be critical, and therefore aborts the certificate issuance or renewal. For hooks that can fail without causing a problem, you will want to indicate that with `allow_failure = true`.


### StdIn / Stdout / Stderr Support

ACMEd supports `stdin`, `stdout`, and `stderr` usage with its hooks. For example, it is possible to write a string to the hook command.  ACMEd also supports a `stdin_str` field specifically for strings, but it is mutually exclusive with `stdin`. Here's an example:

```
[[hook]]
name = "example-hook-2"
type = ["post-operation"]
allow_failure = true
cmd = "sed"
args = [
    "-e",
    "s/CUSTNAME/Steve/",
]
stdin_str = """Congrates CUSTNAME:
Your ssl certificate is now active.
"""
stdout = "/tmp/cust-msg.txt"
```

The above hook reads a string as input, executes the command sed -e, and writes the contents of the standard output to the file /tmp/cust-msg.txt.


### Hook Groups

In some cases, it is helpful to run multiple hook commands instead of specialized scripts. ACMEd makes this possible by grouping hooks, which execute in the order that they are defined.

```
[[group]]
name = "example-group"
hooks = ["example-hook-1", "example-hook-2"]
```

Hook Groups are considered to be just another Hook, and are called just like any other normal hook. So a Hook Group can be called just like any other hook, and a hook group can even call another hook group.

---

## Hook Parameters

ACMEd provides a programmable templating engine that allows custom processing of arguments and stdin/stdout/stderr strings. Numerous parameters can be passed in as arguments to external shell scripts or commands. This is accomplished by re-implementing the [handlebars](https://handlebarsjs.com)  template engine.

### An example:

```
[[hook]]
name = "http-01-example"
type = ["challenge-http-01"]
cmd = "echo"
args = ["{ proof }"]
stdout = "{{ if env.HTTP_ROOT }}{{ env.HTTP_ROOT }}{{ else }}/var/www{{ endif }}/{{ identifier }}/.well-known/acme-challenge/{{ file_name }}"
```

This example uses the `echo` command in an `http-01` challenge to write the `proof` string to a file presentable by the web server as the <domain.com>/.well-known/acme_challenge/<file_name> file.

### Simple Processing

ACMEd support simple if than statements to aid in certificate processing. In the example you can see how the env.HTTP_ROOT parameter is used if it is defined, otherwise `/var/www` is used. This processing allows for great flexibility.


##Available parameters:

### `challenge-dns-01` And `challenge-dns-01-clean`

- `challenge` : string indicating the type of challenge ( dns-01)
- `env` : associative array of strings containing environment variables
- `identifier` : string representing the name of the identifier concerned
- `is_clean_hook` : boolean set to false if it is challenge-dns-01and to true if it ischallenge-dns-01-clean
- `proof` : content of the proof to be written in the TXT field of the DNS zone for the subdomain_acme-challenge

### `challenge-http-01` And `challenge-http-01-clean`

- `challenge` : string indicating the type of challenge ( http-01)
- `env` : associative array of strings containing environment variables
- `file_name` : name of the file to contain the proof (does not include .well-known/acme-challenge/)
- `identifier` : string representing the name of the identifier concerned
- `is_clean_hook` : boolean set to false if it is challenge-http-01and to true if it ischallenge-http-01-clean
- `proof` : content of the proof to be written infile_name

### `challenge-tls-alpn-01` And `challenge-tls-alpn-01-clean`

- `challenge` : string indicating the type of challenge ( tls-alpn-01)
- `env` : associative array of strings containing environment variables
- `identifier` : string representing the name of the identifier concerned
- `identifier_tls_alpn` : string representing the name of the identifier concerned in a form suitable for the TLS-ALPN challenge
- `is_clean_hook` : boolean set to false if it is challenge-tls-alpn-01and to true if it ischallenge-tls-alpn-01-clean
- `proof` : string representing the contents of the extension acmeIdentifierto be used in the self-signed certificate

### `file-post-create`, `file-post-edit`, `file-pre-create` And `file-pre-edit`

- `env` : associative array of strings containing environment variables
- `file_directory` : path to the folder where the file in question is located
- `file_name` : name of the file concerned
- `file_path` : full path of the file concerned

### post-operation

- `env` : associative array of strings containing environment variables
- `identifiers` : array of strings representing the name of the certificate identifiers
- `is_success` : boolean indicating whether the operation was successful or not
- `key_type` : string representing the type of asymmetric key used
- `status` : human readable description of the operation status

### Important Notes: 

The `cmd` field is not an input to the template engine.

### Reversing Paths:

Some web tools or organizations require domain names in reverse order:  `org.example.www` instead of `www.example.org` for example. You can use the special `rev_lables` string formatter in this case.

---

## Common Mistakes

Several issues are common in new instances:

- Access Rights: Pay careful attention to the uid/gid of ACMEd, since hooks will be run with the same uid/gid permissions.
- Create Folders: Writing files to non-existant folders will fail. Use `mkdir -p` if there is a chance the folder does not yet exist.
- Folder Locations: Confirm that the files are being written to the expected location, and that the web server can server files written to that location with ACMEd's uid/gid.
- Unique File/Folder Names: ACMEd runs multiple challenges in parallel. Make sure you're using unique file/folder names so the same files are not being overwritten by simultaneous requests.

