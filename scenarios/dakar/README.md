# "Dakar": The half-installed package

## Description

The checkout platform's local health agent stopped working after a package upgrade was interrupted. The <kbd>checkout-agent</kbd> binary may already be on disk, but its systemd service is not healthy.
<br><br>
Restore the package and service so the agent runs again and still comes back after a reboot. Do not wipe the package database under <i>/var/lib/dpkg</i>, and do not replace the service with a hand-written unit of your own.

## Test

<kbd>checkout-agent</kbd> is <kbd>install ok installed</kbd>; <kbd>checkout-agent.service</kbd> is <kbd>active</kbd> and <kbd>enabled</kbd>; its unit under <i>/lib/systemd/system/</i> is owned by the package.
<br><br>
The "Check My Solution" button runs the script <i>/home/admin/agent/check.sh</i>, which you can see and execute.


**check.sh**

```bash
#!/bin/bash
# DO NOT MODIFY THIS FILE ("Check My Solution" will fail)

status=$(dpkg-query -W -f='${Status}' checkout-agent 2>/dev/null || true)
if [ "$status" != "install ok installed" ]; then
  echo -n "NO"
  exit 0
fi

if ! systemctl is-active --quiet checkout-agent.service 2>/dev/null; then
  echo -n "NO"
  exit 0
fi

if ! systemctl is-enabled --quiet checkout-agent.service 2>/dev/null; then
  echo -n "NO"
  exit 0
fi

# Unit must come from the Debian package, not a hand-rolled override alone
if ! dpkg -S /lib/systemd/system/checkout-agent.service >/dev/null 2>&1; then
  echo -n "NO"
  exit 0
fi

echo -n "OK"
exit 0
```
