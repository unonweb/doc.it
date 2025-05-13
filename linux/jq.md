```sh
loginctl --output=json --no-pager | jq -r '.[].session' # 
# iterate over each element in the array (.[])
# extract .session from each element
# output the values in raw format (-r)
```