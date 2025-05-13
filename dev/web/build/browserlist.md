
- https://github.com/browserslist/browserslist
* https://github.com/browserslist/browserslist#queries
* https://github.com/browserslist/browserslist-example

If you want to change the default set of browsers, we recommend including:
`last 2 versions, not dead, > 0.2%`

This is because `last n versions` on its own does not add popular old versions, while only using a percentage of usage numbers above `0.2%` will in the long run make popular browsers even more popular. We might run into a monopoly and stagnation situation, as we had with Internet Explorer 6. Please use this setting with caution.

```sh
"defaults"  # shortcut for 

"> 0.5%, last 2 versions, Firefox ESR, not dead"
"ChromeAndroid 103" # mobile Chrome version 103.
"Firefox > 20" # desktop Firefox versions newer than 20.
"iOS >= 13.2" # mobile Safari version 13.2 and newer.
"iOS > 15"
```

```json
"browserslist": [
  "cover 99.5%",
  "defaults and fully supports es6-module",
  "maintained node versions"
],
```