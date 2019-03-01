```
cat logs/debug.log  | grep "^201[89]" | awk '{ print $1 }' | uniq
```

```
cat data/raw/labels.csv | cut -d, -f6 | uniq
# -d, says "fields delimited by comma"
# -f6 says "extract field 6"

# This Awk command does same thing:
cat data/raw/labels.csv | awk -F',' '{print $6}' | uniq
```


