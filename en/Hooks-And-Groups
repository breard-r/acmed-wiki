[//]: # (Copying and distribution of this file, with or without modification,)
[//]: # (are permitted in any medium without royalty provided the copyright)
[//]: # (notice and this notice are preserved.  This file is offered as-is,)
[//]: # (without any warranty.)

In order to be able to adapt as much as possible to your environment, ACMEd gives you the possibility to configure the actions to be carried out in certain circumstances. To this end, ACMEd allows you to define shell commands that will be called during certain phases of its execution. These pre-configured shell commands are here called hooks.

---

## The execution phases

Hooks can only be defined for accounts and certificates . When, for these elements, the process reaches certain execution phases, the associated hooks are then triggered. Note that if several hooks have been defined, they are executed in the order in which they were declared.
Accounting Phases

In the context of accounts, the only existing execution phases are those relating to the creation and modification of the file containing the information of the account in question (name, private key, etc.).

- `file-post-create`: Triggered after a file is created
- `file-post-edit`: Triggered after a file is modified
- `file-pre-create`: fires before a file is created
- `file-pre-edit`: fires before a file is modified

---

## Certificate Phases

For certificates, the execution phases are divided into three groups.

First, the execution phases relating to the achievement of the ACME challenges.


- `challenge-dns-01`: Triggered when a DNS-01 challenge is required
- `challenge-dns-01-clean`: fires after the remote server has successfully verified the completion of a DNS-01 challenge
- `challenge-http-01`: Fires when a HTTP-01 challenge is required
- `challenge-http-01-clean`: Fires after the remote server has successfully verified the completion of an HTTP-01 challenge
- `challenge-tls-alpn-01`: Triggered when a TLS-ALPN-01 challenge is required
- `challenge-tls-alpn-01-clean`: fires after the remote server has successfully verified the completion of a TLS-ALPN-01 challenge

Then, just like for accounts, there are the execution phases relating to the creation or modification of certificate files and associated private keys.

- `file-post-create`: Triggered after a file is created
- `file-post-edit`: Triggered after a file is modified
- `file-pre-create`: fires before a file is created
- `file-pre-edit`: fires before a file is modified

Finally, there is a balance sheet execution phase.

- `post-operation`: Triggered at the end of a certificate creation or renewal request

---

## Using hooks

Since ACMEd does not perform any action by itself to respond to the challenges necessary to validate certificate identifiers, it is imperative to use hooks to respond to these challenges. Thanks to this system, ACMEd does not make any presuppositions about how your infrastructure is composed: it is you who tell it, thanks to hooks, what commands to launch in order to achieve this objective.

When a certificate has been renewed, you will certainly want to reload the various services that use it. This can be done via different init systems (systemd, SysV init, Upstart, etc.) or by custom means, ACMEd once again makes no presuppositions about your infrastructure and lets you define the commands to run. This hook can also be an opportunity to send a notification to the people administering the system to inform them of the progress of the operation.

Since keys and certificates are critical elements, many people want to save them or even archive their entire history. In some infrastructures, it is necessary to deploy certificates on other machines. It is to meet this wide variety of uses that ACMEd allows you to define hooks relating to the creation and modification of certain files.

Since some usages are very widespread, ACMEd provides a configuration file, included by default, defining commonly used hooks .
Definition of a hook

To define a hook, it is necessary to give it a name, the execution phases during which it must be triggered, the command to launch as well as any parameters of the latter. Note that the execution phases are entered in the table type.

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

This hook will therefore execute the command echo -e "Hello World!"at the end of a request to create or renew a certificate using this hook.

### Runtime error

By default, when a hook command ends with a return code indicating an error, ACMEd considers that the error impacts the entire action in progress (certificate renewal) and will therefore interrupt the latter. In cases where a hook must be able to fail without impacting the entire action, it is possible to indicate allow_failure = true.

### Inputs and outputs

Programs have a standard input, a standard output and an error output. Hook commands are no exception and it is therefore possible to indicate, using the parameters stdin, stdoutand stderr, the path to a file on which the input or output will be connected. For the standard input, it is also possible to directly indicate a string that will be written on it using stdin_str. Of course, stdinand stdin_strare mutually exclusive.

```
[[hook]]
name = "example-hook-2"
type = ["post-operation"]
allow_failure = true
cmd = "sed"
args = [
    "-e",
    "s/Ceci/Voici/",
    "-e",
    "s/est/une/",
]
stdin_str = """Ceci est
une chaîne de caractères
écrite sur plusieurs
lignes.
"""
stdout = "/tmp/example-hook.txt"
```

The above hook reads a string as input, executes the command cat -e, and writes the contents of the standard output to the file /tmp/example-hook.txt. After the hook is executed, this file will therefore contain the following content:

```
Voici une
une chaîne de caractères
écrite sur plusieurs
lignes.
```

### The groups

In some cases, it is necessary to run multiple commands to achieve a single goal. Each of these commands is a separate hook, so it can be tedious to define all of these commands in each account or certificate that uses them. To avoid this, it is possible to group several hooks into one by defining a group.

A group is defined with a name and the list of hooks it groups. As usual, the hooks will be executed in the order in which they were defined.

```
[[group]]
name = "example-group"
hooks = ["example-hook-1", "example-hook-2"]
```

Groups are considered hooks. As such, it is possible to specify the name of a group instead of the name of a hook in an account, a certificate or even another group. If you look at the contents of the file defining the predefined hooks mentioned above, you will notice that these hooks are actually groups grouping several hooks.

---

## The execution environment

While it is very useful to be able to run predefined commands, this is completely insufficient. Indeed, if it is not possible to retrieve and use detailed information about the context in which the command must be executed, it is useless. For example, to solve an ACME challenge, it is imperative to know what identifier it is, the proof and so on.

The solution that was chosen is to use a template engine that will be used on different elements before the command is executed. The template engine currently used is a re-implementation of handlebars . The elements that will be interpreted by this template engine are the parameters of the command ( args) as well as the inputs/outputs ( stdin, stdin_str, stdout, and stderr). The command itself ( cmd) is not considered a template.

Depending on the execution phase, different variables are accessible from the templates.


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


### Example

```
[[hook]]
name = "http-01-example"
type = ["challenge-http-01"]
cmd = "echo"
args = ["{ proof }"]
stdout = "{{ if env.HTTP_ROOT }}{ env.HTTP_ROOT }{{ else }}/var/www{{ endif }}/{ identifier }/.well-known/acme-challenge/{ file_name }"
```

This hook uses the command echoto, during an http-01 challenge, write the proof to a file that will be served by the web server. The proof is therefore passed as a parameter to the command and the standard output is redirected to the file in which to write it.

The path of this output file is particularly interesting. First, the part {{ if env.HTTP_ROOT }}{ env.HTTP_ROOT }{{ else }}/var/www{{ endif }}allows, if the environment variable HTTP_ROOTis defined, to use it as the root of the web server and, otherwise, to use /var/www. Then, the part /{ identifier }requires having a subfolder containing the name of the identifier from where the web server serves the files relating to this domain name. Finally, the part /.well-known/acme-challenge/{ file_name }concerns the path that will be used in the URL.

Good to know: sometimes, instead of using a folder in the domain name (for example www.example.org), the domain labels are put in reverse order (so, to take the previous example, org.example.www). To handle these cases, you can apply the formatter rev_labels: { identifier | rev_labels }.

---

## Common Mistakes

Incorrectly defined hooks are the number one cause of errors in ACMEd. Humans being human, most errors should be caused by one of the following reasons.

- Access rights: Commands are executed by ACMEd and therefore by the user who launched it. Pay attention to the user, group, rights and any ACLs.
- Creating folders: Creating a file in a folder that does not exist will generate an error. Consider doing a mkdir -pif you expect a folder to exist.
- Bad interface with the web server: If you are not careful, it is easy, for example for the HTTP-01 challenge, to have a difference between the path where ACMEd writes the proof and the path that will be served by the web server.
- Collision: Since ACMEd can run multiple challenges simultaneously, including for the same identifier, it is important to ensure that the behavior of hooks cannot affect other hooks. This is especially true in "cleanup" hooks whose role is to remove evidence once the challenge has been completed.
