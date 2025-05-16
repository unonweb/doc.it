
```bash
# increment
no=0
((no++)) # increases the variable itself
echo $no # 1

echo $((no+2)) # 3 --> does not increase the variable itself
echo $no # 1
```

## additions

```bash
arr=(1,2,3,4,5)
count=1
count=$((count + ${#arr[@]}))

echo $count # 6
```
# integer comparison

`<` and `>` inside `[[ ... ]]` compare strings, not numbers!

```sh
[[ val1 -eq val2 ]] or val1 = val2        # is equal
[[ val1 -ne val2 ]] or val1 != val2       # not equal
[[ val1 -gt val2 ]] or ((val1 > val2))    # greater than
[[ val1 -ge val2 ]] or ((val1 >= val2))   # greater or equal
[[ val1 -lt val2 ]] or ((val1 < val2))    # less than
[[ val1 -le val2 ]] or ((val1 <= val2))   # less or equal
```