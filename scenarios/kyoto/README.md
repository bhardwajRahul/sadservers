# "Kyoto": The Gion ticket API will not start

## Description

The Gion ticket office API should answer on port <kbd>:80</kbd>. After last night's change the office never came back.
<br><br>
Manifests are in <i>/home/admin/app</i>. There is a short handover note in <i>/home/admin/HANDOVER.txt</i>. Wait until the Kubernetes node is Ready after boot.
<br><br>
<kbd>GET /health</kbd> must return <kbd>{"status":"ok","office":"gion-tickets"}</kbd>. Do not move the API off port 80.
<br><br>
<b>TIP:</b> You can use <kbd>k</kbd> as an alias for <kbd>kubectl</kbd>, and it has autocomplete enabled.

## Test

<kbd>curl -s http://127.0.0.1/health</kbd> returns <kbd>{"status":"ok","office":"gion-tickets"}</kbd> and the <i>gion-api</i> pod in the <i>gion</i> namespace is Ready (<kbd>1/1</kbd>).
<br><br>
The "Check My Solution" button runs the script <i>/home/admin/agent/check.sh</i>, which you can see and execute.


**check.sh**

```bash
#!/bin/bash
# DO NOT MODIFY THIS FILE ("Check My Solution" will fail)

fail() { echo -n "NO"; exit 0; }

export KUBECONFIG="${KUBECONFIG:-/etc/rancher/k3s/k3s.yaml}"

body=$(curl -sS -m 1 http://127.0.0.1/health 2>/dev/null) || fail
echo "$body" | grep -q 'gion-tickets' || fail

ready=$(kubectl get pods -n gion -l app=gion-api \
  -o jsonpath='{.items[0].status.containerStatuses[0].ready}' --request-timeout=1s 2>/dev/null) || fail
[ "$ready" = "true" ] || fail

echo -n "OK"
exit 0
```
