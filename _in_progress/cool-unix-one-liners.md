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


```
# Print out header names one per row (instead of a million jammed on one row)
head -n 1 tons_of_data_fields.csv | tr ',' '\n
```

```
# cat: spit out file contents
# cut -d, -f6:  incoming stream has comma-separated columns; only let col6 pass
# uniq: only show unique rows from incoming stream
# grep -v Description: remove incoming rows w/ "Description"
# cat -n: print out incoming stream w/ numbered rows
!cat data.csv | cut -d, -f6 | uniq | grep -v Description | cat -n
```
