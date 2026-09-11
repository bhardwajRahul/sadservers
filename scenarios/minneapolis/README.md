# "Minneapolis": Break a CSV file

## Description

Break the Comma Separated Valued (CSV) file <i>data.csv</i> in the <i>/home/admin/</i> directory into exactly 10 smaller files of about the same size named <i>data-00.csv</i>, <i>data-01.csv</i>, ... , <i>data-09.csv</i> files in the same directory. All the files should have the same header (first line with column names) as <i>data.csv</i>. None of the smaller files should be bigger than 32KB.
<br><br>Note: to simplify, disregard broken lines in your files (ie, you can break a file at any point, not just at a newline). The resulting files don't have to be proper CSV files.

## Test

There are exactly ten files <i>data-00.csv</i> … <i>data-09.csv</i> in <i>/home/admin/</i>. Each starts with the same header as <i>data.csv</i>, is at most 32KB, and has enough content (not a stub).
<br><br>
The "Check My Solution" button runs the script <i>/home/admin/agent/check.sh</i>, which you can see and execute.


**check.sh**

```bash
#!/usr/bin/bash
# DO NOT MODIFY THIS FILE ("Check My Solution" will fail)

cd /home/admin

norm_header() {
    local h=$1
    h=${h#$'\xef\xbb\xbf'}
    h=${h%$'\r'}
    printf '%s' "$h"
}

expected_header=$(norm_header "$(head -n 1 data.csv)")
threshold=$((32 * 1024))
minlines=100

for i in {0..9}; do
    file="data-0$i.csv"

    if [[ -f "$file" ]]; then
        file_header=$(norm_header "$(head -n 1 "$file")")
        if [[ "$file_header" != "$expected_header" ]]; then
            echo -n "NO"
            exit 0
        fi

        filesize=$(stat -c%s "$file")
        if (( filesize > threshold )); then
            echo -n "NO"
            exit 0
        fi

        lines=$(wc -l < "$file")
        if (( lines < minlines )); then
            echo -n "NO"
            exit 0
        fi
    else
        echo -n "NO"
        exit 0
    fi
done

echo -n "OK"
exit 0
```
