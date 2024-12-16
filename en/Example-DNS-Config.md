
Here is an example dns-01 challenge acmed.toml file:

To switch to production, just change the `endpoint` line in `[[certificate]]` to `letsencrypt v2 production`.

This example has backed off the Let's Encrypt published limit of 20 / sec to just 1 per sec.


```
[global]
accounts_directory = "/tmp/acc"
certificates_directory = "/tmp/certs"

[[rate-limit]]
name = "LE min"
number = 1
period = "1s"

[[endpoint]]
name = "letsencrypt v2 staging"
url = "https://acme-staging-v02.api.letsencrypt.org/directory"
rate_limits = ["LE min"]
tos_agreed = true

[[endpoint]]
name = "letsencrypt v2 production"
url = "https://acme-v02.api.letsencrypt.org/directory"
rate_limits = ["LE min"]
tos_agreed = true

[[account]]
name = "xxx"
email = "it@xxx.com"

[[hook]]
name = "cleanup-dns-01-hook"
type = ["challenge-dns-01-clean"]
cmd = "cleanup.sh"
args = [ "{{ identifier }}" ]


[[hook]]
name = "auth-dns-01-hook"
type = ["challenge-dns-01"]
cmd = "auth.sh"
args = [ "{{ identifier }}", "{{ proof }}" ]


[[certificate]]
account = "xxx"
endpoint = "letsencrypt v2 staging"
domains = [{ challenge = "dns-01", dns = "*.xxx.net" }]
hooks = ["auth-dns-01-hook", "cleanup-dns-01-hook"]
```

---

auth.sh example:

```
#!/bin/bash

# Script to add DNS TXT record using NameSilo's API:

wildcard=$1
proof=$2

acme="_acme-challenge"
dom=${wildcard#"*."}

rslt="`curl -s \"https://www.namesilo.com/api/dnsAddRecord?version=1&type=xml&key=<--namesilo key-->&domain=${dom}&rrtype=TXT&rrhost=${acme}&rrvalue=${proof}&rrttl=3600\"`"

# Sleep 1/2 hour for the cert to propagate
sleep 1800

```

---

cleanup.sh example:

```
#!/bin/bash

wildcard=$1

dom=${wildcard#"*."}

while true; do
  js="`curl -s \"https://www.namesilo.com/api/dnsListRecords?version=1&type=json&key=<--namesilo key-->&domain=${dom}\"`"

  record_id=$(echo "$js" | jq -r '.reply.resource_record[] | select(.type == "TXT" and (.host | tostring | startswith("_acme-challenge"))) | .record_id' | head -n 1)

  if [ -z "$record_id" ]; then
    break
  fi

  rslt="`curl -s \"https://www.namesilo.com/api/dnsDeleteRecord?version=1&type=xml&key=<--namesilo key-->&domain=${dom}&rrid=$record_id\"`"
  sleep 20
done
```


