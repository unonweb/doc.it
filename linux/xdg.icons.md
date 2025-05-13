## xdg-icon-resource

- append a custom icon to an existing icon theme 
- will resize and copy the icon to `$HOME/.local/share/icons/`

```sh
# add as emblem:
xdg-icon-resource install --size 128 --context emblems archuser-example.png
# add as normal icon:
xdg-icon-resource install --size 128 archuser-example.png
```