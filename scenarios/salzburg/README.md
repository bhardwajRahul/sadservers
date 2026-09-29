# "Salzburg": The TLS key that still matches

## Description
The Salzburg Festival box-office site should be served over HTTPS on port <kbd>:443</kbd>. The certificate expired, and nginx will not start.
<br><br>
We still have the public key at <i>/home/admin/festival.pub</i>. A key-archive cleanup left many leftover private keys under <i>/opt/festival/keys</i>. Find the private key that matches that public key and copy it to <i>/home/admin/festival.key</i>. Issue a new certificate for the same host using that same key (do not generate a new key pair) and get the site serving HTTPS again.
<br><br>
There is a short handover note in <i>/home/admin/HANDOVER.txt</i>.

## Test
<i>/home/admin/festival.key</i> and <i>/home/admin/festival.pub</i> are a matching key pair.
<br><br>
<kbd>curl -k https://127.0.0.1/</kbd> returns the box-office page.
<br><br>
The certificate presented on <kbd>:443</kbd> is not expired, its subject is the original festival host, and it was issued with the same key as <i>/home/admin/festival.pub</i>.
<br><br>
The "Check My Solution" button runs the script <i>/home/admin/agent/check.sh</i>, which you can see and execute.


**check.sh**

```bash
#!/bin/bash
# DO NOT MODIFY THIS FILE ("Check My Solution" will fail)

fail() { echo -n "NO"; exit 0; }

PUB=/home/admin/festival.pub
KEY=/home/admin/festival.key

[ -r "$KEY" ] && [ -r "$PUB" ] || fail

key_pub=$(openssl pkey -in "$KEY" -pubout 2>/dev/null) || fail
want_pub=$(openssl pkey -pubin -in "$PUB" -pubout 2>/dev/null) || fail
[ -n "$key_pub" ] && [ "$key_pub" = "$want_pub" ] || fail

crt=$(mktemp)
trap 'rm -f "$crt"' EXIT

echo | timeout 1 openssl s_client -connect 127.0.0.1:443 \
  -servername www.festival.salzburg.local 2>/dev/null \
  | openssl x509 -out "$crt" 2>/dev/null || fail
[ -s "$crt" ] || fail

end=$(openssl x509 -in "$crt" -noout -enddate 2>/dev/null | cut -d= -f2) || fail
end_ts=$(date -d "$end" +%s) || fail
now=$(date +%s)
[ "$now" -lt "$end_ts" ] || fail

crt_pub=$(openssl x509 -in "$crt" -noout -pubkey 2>/dev/null) || fail
[ "$crt_pub" = "$want_pub" ] || fail

subj=$(openssl x509 -in "$crt" -noout -subject 2>/dev/null | tr -d '[:space:]')
[[ "$subj" == *"CN=www.festival.salzburg.local"* ]] || fail
[[ "$subj" == *"O=SalzburgFestival"* ]] || fail
[[ "$subj" == *"C=AT"* ]] || fail

echo -n "OK"
exit 0
```
