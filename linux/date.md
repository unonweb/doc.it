# LINKS

- https://wiki.ubuntuusers.de/date/

# EXAMPLES

```sh
date +%Y-%m-%d # 2024-07-31
date +%Y/%m/%d # 2024/07/31
date +%y-%m-%d # 24-07-31

date +%y-%m-%d_%R # 25-04-14_14:28
touch "/tmp/test_$(date +%y-%m-%d_%R)"
```