# install

```js
var CleanCSS = require('clean-css');
var input = 'a{font-weight:bold;}';
var options = { /* options */ };
var output = new CleanCSS(options).minify(input);
```

```js
new CleanCSS().minify(['path/to/file/one', 'path/to/file/two']); // optimize multiple files
```

```js
const CleanCSS = require("clean-css");

const output = new CleanCSS().minify(`
  a {
    color: blue;
  }
  div {
    margin: 5px
  }
`);

console.log(output);

// Log:
{
  styles: 'a{color:#00f}div{margin:5px}',
  stats: {
    efficiency: 0.6704545454545454,
    minifiedSize: 29,
    originalSize: 88,
    timeSpent: 6
  },
  errors: [],
  inlinedStylesheets: [],
  warnings: []
}
```