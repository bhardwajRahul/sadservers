# "Bucharest": Connecting to Postgres

## Description

A web application relies on the PostgreSQL database on this server. After a client-authentication change, that connection fails.
<br><br>
The application connects over TCP to <kbd>127.0.0.1</kbd>, database <i>app1</i>, user <i>app1user</i>, password <i>app1user</i>. The role and password are already set. Do not replace the database or the account; restore this connection. It must still work after a reboot.


## Test

This command succeeds (exits 0, no error):
<br>
<kbd>PGPASSWORD=app1user psql -h 127.0.0.1 -d app1 -U app1user -c '\q'</kbd>
<br><br>
The "Check My Solution" button runs the script <i>/home/admin/agent/check.sh</i>, which you can see and execute.


**check.sh**

```bash
#!/bin/bash
# DO NOT MODIFY THIS FILE ("Check My Solution" will fail)

if timeout 1 env PGPASSWORD=app1user psql -w -h 127.0.0.1 -d app1 -U app1user -c '\q' >/dev/null 2>&1; then
  echo -n "OK"
else
  echo -n "NO"
fi
exit 0
```
