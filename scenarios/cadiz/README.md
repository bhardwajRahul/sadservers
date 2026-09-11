# "Cadiz": Cut the live wires

## Description

Your host is wired into a demolition circuit. <kbd>cable</kbd> processes are the wires. They talk only to a root supervisor, <kbd>fuse</kbd>, to keep it convinced the circuit is intact. Some cables are live; the others are decoys. <kbd>deton</kbd> processes (also root) are the charges. They talk only to <kbd>fuse</kbd>, never to the cables. That supervisor is what fires them. You cannot kill <kbd>deton</kbd> or <kbd>fuse</kbd>. You do not have general root access. You can only cut cables.
<br><br>
To cut a cable, <kbd>kill -9</kbd> it. Cut all the live cables (and only those), and cut them together. Any other cut resets the circuit: <kbd>fuse</kbd> fires the <kbd>deton</kbd> charges and rolls a new live set.


## Test

The charges are gone (<kbd>deton</kbd> has exited) and the root-owned stamp <i>/var/lib/fuse/disarmed</i> exists. That stamp is created only by the supervisor when the live wires drop together. Creating a file in your home directory does not count. The stamp remains across reboot.
<br><br>
The "Check My Solution" button runs the script <i>/home/admin/agent/check.sh</i>, which you can see and execute.


**check.sh**

```bash
#!/bin/bash
# DO NOT MODIFY THIS FILE ("Check My Solution" will fail)

stamp=/var/lib/fuse/disarmed
armed=/var/lib/fuse/armed

if [ -f "$stamp" ] && [ ! -e "$armed" ]; then
  owner=$(stat -c %U "$stamp" 2>/dev/null || true)
  if [ "$owner" = "root" ]; then
    echo -n "OK"
    exit 0
  fi
fi

echo -n "NO"
exit 0
```
