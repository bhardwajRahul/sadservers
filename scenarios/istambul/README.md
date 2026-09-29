# "Istanbul": Delete unattached AWS EBS volumes

## Description
Finance found orphaned EBS volumes racking up cost in this account. Your job is to find every volume that is <b>not attached</b> to an EC2 instance and <b>delete</b> it.
<br><br>
Do <b>not</b> delete volumes that are attached (<kbd>in-use</kbd>). Do <b>not</b> stop or terminate instances.
<br><br>
The AWS CLI is already configured against the local endpoint. Start with:
<br>
<kbd>aws ec2 describe-volumes</kbd>
<br><br>
NOTE: this scenario was done using Floci, a local AWS emulator; there are no real AWS resources.

## Test
Delete every EBS volume that is <b>not</b> attached to an EC2 instance. Attached (<kbd>in-use</kbd>) volumes and running instances must remain.
<br><br>
The "Check My Solution" button runs the script <i>/home/admin/agent/check.sh</i>, which you can see and execute.


**check.sh**

```bash
#!/bin/bash
# DO NOT MODIFY THIS FILE ("Check My Solution" will fail)

export AWS_ACCESS_KEY_ID="${AWS_ACCESS_KEY_ID:-test}"
export AWS_SECRET_ACCESS_KEY="${AWS_SECRET_ACCESS_KEY:-test}"
export AWS_DEFAULT_REGION="${AWS_DEFAULT_REGION:-us-east-1}"
export AWS_EC2_METADATA_DISABLED=true
ENDPOINT="${AWS_ENDPOINT_URL:-http://127.0.0.1:4566}"

aws_ec2() {
  aws ec2 --endpoint-url "$ENDPOINT" "$@"
}

# Fail closed if Floci is down or awscli missing
if ! aws_ec2 describe-volumes >/dev/null 2>&1; then
  echo -n "NO"
  exit 0
fi

available=$(aws_ec2 describe-volumes \
  --filters Name=status,Values=available \
  --query 'length(Volumes)' --output text 2>/dev/null)

in_use=$(aws_ec2 describe-volumes \
  --filters Name=status,Values=in-use \
  --query 'length(Volumes)' --output text 2>/dev/null)

# Treat None/empty as 0
available=${available:-0}
in_use=${in_use:-0}
[[ "$available" == "None" ]] && available=0
[[ "$in_use" == "None" ]] && in_use=0

# Win: no orphan (available) volumes; keep at least one attached volume
if [[ "$available" -eq 0 && "$in_use" -ge 1 ]]; then
  echo -n "OK"
else
  echo -n "NO"
fi
exit 0
```
