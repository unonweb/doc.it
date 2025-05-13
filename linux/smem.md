# smem

There is one unique thing about smem:
it reports **PSS** (**Proportional Set Size**), a more meaningful representation of memory usage by applications and libraries in a virtual memory setup.

```sh
smem -k | grep strapi
smem -k --processfilter="strapi" # faster
smem -k --processfilter="strapi" --sort="pss" | egrep "pattern|$"
# this egrep trick displays all matches but highlights pattern
```
