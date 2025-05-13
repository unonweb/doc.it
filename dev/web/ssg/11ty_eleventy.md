CLI
======

```sh
eleventy # global version
npx @11ty/eleventy # local version; run inside the project folder <br>
npx @11ty/eleventy --serve
```

# PROJECT STRUCTURE

* **.eleventy.js** - this file is at the root of your folder and helps you configure your project. 
* **_sites** - this folder stores all your statically generated files. It appears after building your project 
* **_includes** - this folder stores your default layout templates.
* **_data** - this folder helps us store data from sources outside Eleventy i.e. JSON files and APIs

# TEMPLATE LANGS

## 11ty.js

* YAML Front Matter is not supported in JavaScript template types. Use data methods instead!

### with class

```js
// index.11ty.js
class Test {
  data() {
    return {
      name: "Ted",
      layout: "teds-rad-layout",
      key: "hello",
      permalink: data => `/my-permalink/${data.key}/`
    }
  }
  // or `async render({name}) {`
  render({name}) {
    // 1. includes the data from above
    // 2. includes the data from the global data   
    return `<p>${name}</p>`;
  }
}

module.exports = Test
```

### no class

```js
/* STYLE 2 */

exports.data = {
	layout: "default.11ty.js",
	title: "My Rad JavaScript Blog Post"
}

exports.render = function (data) {
	return `
		<h1>Hello! Welcome to the Eleventy.</h1>
		<p>This is ${data.title}</p>
		${JSON.stringify(data.articles)}
		`
}
```

```js
// default.11ty.js

module.exports = function(data) {
	return `<!DOCTYPE html>
	<html lang="en">
	  <head>
		<meta charset="UTF-8" />
		<meta content="width=device-width, initial-scale=1.0" name="viewport" />
		<title>
		  ${data.title}
		</title>
	  </head>
	
	  <body>
		  ${data.content}
	  </body>
	</html>`
}

// data.title - gets the front matter variable 'title'
// data.content - gets the content of // index.11ty.js
```

### loops

```js
exports.render = function(data) {
	return `
		${data.articles.map(art => {
			return `<p>${art.content}</p>`
		})}`
}
		
```

### conditional rendering

```js
 module.render = function({ bool: true }) {
    	return `
          <h1>Hello World</h1>
          ${bool ? 
            `<p>Yay! bool is true!</p>`
            :
            `<p>Oh no! bool is false!</p>`
          }
        `;
  	}
 }
```

### as layout

```js
class BaseTemp {

	render(data) {

		return `
		<!DOCTYPE html>
      <html lang="de">
      <head>
        <meta charset="UTF-8" />
        <meta content="width=device-width, initial-scale=1.0" name="viewport" />
        <title>${data.site.title}</title>
        <!-- CSS -->
        <link rel="stylesheet" href="/src/styles.css">
        <!-- JS -->
        <script type="module" async src="/src/scripts.js"></script>
      </head>
      <body>
        ${data.content}
      </body>
			</html>
     `
	}
}

module.exports = BaseTemp
```

## webc

### webc:keep

* **opt-out of asset bundling** for individual elements inside of a component definition

```html
<style webc:keep></style>
<script webc:keep></script>
```

* keep the tag around HTML-only components

```html
<html-only-component webc:keep></html-only-component>
```

* save a `<slot>` for use in a client-side custom element

```html
<slot webc:keep></slot>
```

### webc:scoped

webc:scoped="my-prefix"` >> 11ty will automatically check for duplicate values in your component tree and throw an error if collisions occur.

### @properties

Make any attribute into a prop by prefixing it with `@`.  
Properties are *“private” attributes* that don’t end up in the output HTML (they are private to WebC). 
They are **identical to attributes except that they are filtered from the output HTML**.

```html
<!-- page.webc -->
<my-component @colgap="12px"></my-component>
<!-- component.webc -->
<div @html=`${colgap}`></div> <!-- the same -->
<div @html=colgap></div> <!-- the same -->
<div @html="colgap"></div> <!-- the same -->
```

```js
const properties = [
  (post.description) ? `@data-desc="${post.description}"` : '',	// dash-dash notation!
  (useAsTitle) ? `@use-as-title="${useAsTitle}"` : '', 	// dash-dash notation!
].join(' ')
```

#### @text

```html
<!-- page.webc -->
<my-component @prop="Hello"></my-component>
<!-- component.webc -->
<p @text="prop"></p> <!-- outputs <p>Hello</p> -->
```

* `property-name` converts to `propertyName`

```html
<my-component @property-name="hello"></my-component>
<span @html=propertyName></span>
```

#### @html | @raw

* @html: Content returned from the @html prop will be processed as WebC
* @raw: will prevent processing the result as WebC

### :attributes and :properties

(**using JavaScript for the value instead of a string**)

Make any attribute or property dynamic by prefixing it with a colon (`:`). 
You have access to 

* host component attributes, 
* props
* page data

```html
<!-- page.webc -->
<avatar-image src="my-image.jpeg" alt="Zach is documenting this project" :@dynamic-prop="'hello'"></avatar-image>
<!-- component.webc -->
<img :src="src" :alt="alt" class="avatar-image">

<!-- page.webc -->
<counter-zach @name="first" @value="3"></counter-zach>
<!-- component.webc -->
<output :for="uid" @html="value"></output> <!-- value will be rendered as html within <output> -->
<input type="number" :id="uid" :name="name" :value="value"> <!-- value will be set to an attribute -->
```

```html
---
layout: default.webc
mode: dev
---
<h2 @html="mode"></h2> <!-- <h2>dev<h2>
```



### webc:if

```html
<!-- page.webc -->
<un-button @include-style="3">Click</un-button>
<!-- button.webc -->
<style webc:if="includeStyle === '3'">
  :host {
    color: red;
  }
</style>
```



### webc:is

```html
<div webc:is="my-component"></div>

<!-- equivalent to -->
<my-component></my-component>
```

### webc:setup

* run arbitrary JavaScript
* provide data and markup to your component
* any top level variables declared here are available in your component as local data.
* used to setup variables to be consumed in the template below
* data is scoped to the component and *does not* flow back up in the Data Cascade
* similar to using JS as a Eleventy Front Matter type
* `__filename` doesn't work
* `require()` doesn't work

```html
<script webc:setup>
	const myHtml = `<h1>${frontmatterdata}</h1>`
</script>

<div @raw="myHtml"></div> <!-- @raw does not reprocess as WebC -->
<div @html="myHtml"></div>

<!-- you've got access to eleventy supplied data -->
<script webc:setup>
	console.log(page)
  console.log(collections)
</script>
```

### webc:type=js

(render functions)

* A render function is a `<script>` tag with some special WebC attributes
* Inside the script tag is a function that *returns a string*. That function gets executed and **that string gets written into your markup**.
* Run any arbitrary **server JS** in WebC
* access to the component attributes and props 
* when passing attributes/properties you need to **dash them in the template and camelCase them in the component** 
* outputs the result of the very last statement executed in the script
* will be replaced by that result

```html
---
frontmatterdata: "Hello from Front Matter"
---
<script webc:type="js">
	`<h1>${frontmatterdata}</h1>`
</script>
```

```html
<un-posts @prop-categories="hello from property"></un-posts>

<script webc:if="propCategories" webc:type="js">
  console.log(propCategories)
</script>
```



```html
<!-- Listing all pages in your collections -->
<ul>
  <script webc:type="js" webc:is="template">
    collections.all
      .map((page) => `<li>
          <a href="${page.url}>${page.data.title}</a>
        </li>`)
      .join("");
  </script>
</ul>
```

* renders links to pages from a collection
* accepts a tag name and an optional limit
* produces `<article>` tags for each page in the collection

```html
<!-- collection-list.webc -->
<script webc:type="js" webc:is="template">
  // Copy the collection because Array.prototype.reverse() reverses the array
  // in-place, and we don't want to permanently reverse the collection, we just
  // want to reverse it for this component.
  let collection = [...collections[tag]].reverse();

  if (limit) collection = collection.slice(0, limit);

  collection.map((page) => `<article>
      <a href="${page.url}">${page.data.title}</a>
      <p>${page.data.description}</p>
    </article>`)
  .join("");
</script>

<!-- home.webc -->
<h2>Techniques</h2>
<collection-list tag="technique" limit="3"></collection-list>
```



### webc:type=11ty

* 11ty.js is not yet supported as a template engine for `renderTemplate`. Use `renderFile` instead!

```js
---
frontmatterdata: "Hello from Front Matter"
---
<template webc:type="11ty" 11ty:type="11ty.js">
{% assign t = "Liquid in WebC" %}
## {{ t }}

_{{ frontmatterdata }}_
</template>
```

### front matter

* front matter is supported in page-level templates only (`.webc` files in your input directory) 
* not in components

```yaml
---js
{
	layout: 'default',
	pagination: {
		data: 'subpages.docs', // <-- must be a string!!!
		size: 1,
	},
	permalink: (data) => `/${data.pagination.items[0].slug}/index.html`,
}
---
```

```yaml
---
layout: default
pagination:
  data: subpages.docs
  size: 1
permalink: (data) => `/${data.pagination.items[0].slug}/index.html`
---
```

### bundles

Eleventy runs WebC in *Bundler mode*. That means that when it finds `<style>`, `<link rel="stylesheet">`, or `<script>` elements in component definitions they are removed from the output markup and *their content* is aggregated together for re-use in asset bundles on the page.

```html
<!-- inline bundles -->
		<style @raw="getBundle('css')" webc:keep></style>
		<script @raw="getBundle('js')" webc:keep></script>

		<
```

#### fill buckets

```html
<style>/* This CSS is put into the default bucket */</style>
<script>/* This JS is put into the default bucket */</script>
<style webc:bucket="defer">/* This CSS is put into the `defer` bucket */</style>
<script webc:bucket="defer">/* This JS is put into the `defer` bucket */</script>
```

#### empty buckets

```html
<!doctype html>
<html lang="en">
	<head>
		<meta charset="utf-8">
		<title>WebC Example</title>

		<!-- inline bundles -->
		<style @raw="getBundle('css')" webc:keep></style>
		<script @raw="getBundle('js')" webc:keep></script>

		<!-- or write your bundle to a file -->
		<link rel="stylesheet" :href="getBundleFileUrl('css')">
		<script :src="getBundleFileUrl('js')"></script>
	</head>
	<body @raw="content"></body>
</html>
```

* writing bundles to files will likely be slower for empty-cache first time visitors but better cached in the browser for repeat-views (and across multiple pages, too).
* **inline critical styles**
* **defer non-critical styles**

#### modify the bundle output

You can wire up your own async-friendly callbacks to transform the bundle output too. Here’s a quick example of [`postcss` integration](https://github.com/postcss/postcss#js-api).

```js
const bundlerPlugin = require("@11ty/eleventy-plugin-bundle");
const postcss = require("postcss");
const postcssNested = require("postcss-nested");

module.exports = function(eleventyConfig) {
	eleventyConfig.addPlugin(bundlerPlugin, {
		transforms: [
			async function(content) {
				// this.type returns the bundle name.
				if (this.type === 'css') {
					// Same as Eleventy transforms, this.page is available here.
					let result = await postcss([postcssNested]).process(content, { from: this.page.inputPath, to: null });
					return result.css;
				}

				return content;
			}
		]
	});
};
```

#### webc:keep

```html
<style>/* This is bundled. */</style>
<style webc:keep>/* Do not bundle me—leave as is */</style>
```

# COLLECTIONS

`eleventyImport.collections` is an Array of collection or tag names used by the template. 
This is used to inform both `--incremental` builds and template build order.

Just as an example, say your blog index page iterates over `collections.posts`. If you add `eleventyImport.collections: ["posts"]` to the blog index page data (somewhere in the data cascade), it will  rebuild the index page when any of your blog posts are changed.

```js
exports.data = function() {
  return {
    eleventyImport: {
      collections: ["post"]
    }
  }
};
exports.render = function(data) {
  return `<ul>
    ${data.collections.post.map(post => `<li>${post.data.title}</li>`).join("\n")}
  </ul>`;
};
```



# PAGINATION

```js
class Publications {
    data() {
        return {
            layout: 'base',
			pagination: {
				size: 1,
				data: 'strapi.publications', // <-- this comes from ./_data/strapi.js
                // (must be a string)
			},
			//permalink: "/en/publications.html",
        }
    }
}

// use variables in front matter data

exports.data = {
	permalink: data => `/${data.page.fileSlug}/index.html`,
}
```

### pagination object

```js
{
  items: [], // Array of current page’s chunk of data
  pageNumber: 0, // current page number, 0 indexed

  // Cool URLs
  hrefs: [], // Array of all page hrefs (in order)
  href: {
    next: "…", // put inside <a href="">Next Page</a>
    previous: "…", // put inside <a href="">Previous Page</a>
    first: "…",
    last: "…",
  },

  pages: [], // Array of all chunks of paginated data (in order)
  page: {
    next: {}, // Next page’s chunk of data
    previous: {}, // Previous page’s chunk of data
    first: {},
    last: {}},
  }
}
```

### paging objects

```js
class Publications {
    data() {
        return {
            layout: 'base',
						pagination: {
						size: 1,
						data: 'strapi.publications', // <-- this comes from ./_data/strapi.js (must be a string)
        		resolve: values, 
              // add this if you'd like the pagination to iterate over the values 
              // instead of the keys (using Object.values instead of Object.keys)
						},
        }
    }
}

data.pagination.items[0] // holds the object key
```

```js
class Pages {
	data() {
		return {
			layout: 'html',
			pagination: {
				data: 'pages',
				size: 1,
			},
			permalink: data => {
				return `/${data.pagination.items[0]}/index.html` // we're paging an obj, so this is inserting the object KEY
				}
			},
		}
	}

	render(data) {
    let key = data.pagination.items[0]
    let value = data.pages[key]
		console.log(value)
	}
```



### inject data into pagination

```js
/* don't use this as eleventyComputed is resolved AFTER pagination
instead use global data! */
class Pages {
    async data() {
		
		const pages = await queryPL(`${site.apiUrl.dev}/pages`, site.apiKey, { slug: { not_equals: '' } } )
		const headers = await queryPL(`${site.apiUrl.dev}/headers`, site.apiKey)

        return {
            layout: 'html',
						tags: 'pages',
            pages: pages.docs, // docs!
				    pagination: {
        	    data: 'pages',
                size: 1,
            },
            permalink: data => `/${data.pagination.items[0].slug}/index.html`,
						eleventyComputed: {
							headers: headers
						}
        }
    }
}
```



DATA
======


data cascade
--------------------------------

When the data is merged in the *Eleventy Data Cascade*, the order of priority for sources of data is (from highest priority to lowest):

1. Computed Data
2. Front Matter in Templates
3. Template Data Files
4. Directory Data Files (and ascending Parent Directories)
5. Front Matter in Layouts
6. Configuration API Global Data
7. Global Data Files

## Front matter (2)

Add data in your template front matter, like this:

```yaml
---  
title: My page title
tags: ['doc', 'md']
---
```
The above is using YAML syntax. You can use other formats too.
Locally assigned front matter values override things further up the layout chain. Note also that layouts can contain front matter variables that you can use in your local template.

```js
// Normally, front matter does not support template syntax, but `permalink` does:
exports.data = {
	permalink: data => `/${data.page.fileSlug}/index.html`,
}
```

## local data files (4 + 5)

* **Template**: if you want some data to be available locally *only to one specific template file* use a JSON file named after that template file
  `second-blog-post.json` for `second-blog-post.md`
* **Directory**: if you want some data to be available to *a whole directory of templates* use a JSON file inside of that directories and name it after the directory `my_blog_posts.json` for `my_blog_posts/`  
* The name of the data file must match either the post or the directory it resides within (therefore *local* data files)

## addGlobalData() (6)

```js
eleventyConfig.addGlobalData('pages', async () => {
  //const pages = await queryPL(`${site.apiUrl.dev}/pages`, site.apiKey) // get all pages
  const pages = await queryLocalAPI( { slug: 'pages', siteID: site.siteID } )
  return pages.docs
})
eleventyConfig.addGlobalData('posts', async () => {
  return await queryPL(`${site.apiUrl.dev}/posts`, site.apiKey) // get all posts
})
eleventyConfig.addGlobalData('headers', async () => {
  return await queryPL(`${site.apiUrl.dev}/headers`, site.apiKey)
})
eleventyConfig.addGlobalData('menus', async () => {
	return await queryPL(`${site.apiUrl.dev}/menus`, site.apiKey)
})
```

## global data files (7)

Your global data folder is controlled by the `dir.data` configuration option. 
All *.json and `module.exports` values from *.js files in this directory will be added into a global `data` object available to all templates.

```json
// _data/userList.js
module.exports = [
  "user1",
  "user2"
]

// If you return a function, we’ll use the return value from that function.
module.exports = function() {
  return [
    "user1",
    "user2"
  ]
}


```


Eleventy supplied data
------------------------------

* `pkg`: The local project’s package.json values.
* `pagination`, when enabled using pagination in front matter.
* `collections`: Lists of all of your content, grouped by tags.
* `page` # Has information about the current page.
  * `page.url` # useful for finding the current page in a collection. 
  * `page.date` # The date associated with the page. Defaults to the content’s file created date but can be overridden

#### page

```js
let page = {

  // URL can be used in <a href> to link to other templates
  // Note: This value will be `false` if `permalink` is set to `false`.
  url: "/current/page/myFile/",

  // For permalinks: inputPath filename minus template file extension
  fileSlug: "myFile",

  // For permalinks: inputPath minus template file extension
  filePathStem: "/current/page/myFile",

  // JS Date Object for current page (used to sort collections)
  date: new Date(),

  // The path to the original source file for the template
  // Note: this will include your input directory path!
  inputPath: "./current/page/myFile.md",

  // Depends on your output directory (the default is _site)
  // You probably won’t use this: `url` is better.
  // Note: This value will be `false` if `permalink` is set to `false`.
  outputPath: "./_site/current/page/myFile/index.html",

  // Added in 1.0
  // Useful with `page.filePathStem` when using custom file extensions.
  outputFileExtension: "html",

  // Available in 2.0 with the i18n plugin
  // The default is the value of `defaultLanguage` passed to the i18n plugin
  lang: "",
};
```

### collections

#### the special `all` collection

By default Eleventy puts all of your content (independent of whether or not it has any assigned tags) into the `collections.all `collection. This allows you to iterate over all of your content inside of a template.

#### collection item data structure

* `item.inputPath`      # the full path to the source input file (including the path to the input directory)
* `item.fileSlug`       # Mapped from the input file name, useful for permalinks. Read more about fileSlug.
* `item.outputPath`     # the full path to the output file to be written for this content
* `item.url`            # url used to link to this piece of content.
* `item.date`           # the resolved date used for sorting. Read more about Content Dates.
* `item.data`           # all data for this piece of content (includes any data inherited from layouts)
* `item.data.title`     # only available if given in the meta section!
* `item.templateContent`# the rendered content of this template. This does not include layout wrappers.

CONFIG
===================================================

## file structure

```js
dir: {
  input: "." // thetop level directory/file/glob used to look for templates.
  output: "_site"
  data: "_data" // directory with the global data template files, available to all templates
  includes: "_includes" // Layouts, include files, extends files, partials, or macros. These files will not be processed as full template files, but can be consumed by other templates. Relative to your input directory.
}
pathPrefix: "/" // If your site lives in a different subdirectory (particularly useful with GitHub pages), use pathPrefix to specify this. It’s used by the url filter and inserted at the beginning of all absolute url href links. It does not affect your file structure. Leading or trailing slashes are all normalized away, so don’t worry about it.
```
## templates

```js
dataTemplateEngine: "false" // The dir.data global data files run through this template engine before transforming to JSON.
markdownTemplateEngine: "liquid" // Markdown files run through this template engine before transforming to HTML.
htmlTemplateEngine: "liquid" // HTML templates run through this template engine before transforming to (better) HTML.
templateFormats: ["html", "liquid", "ejs", "md", "hbs", "mustache", "haml", "pug", "njk", "11ty.js"] // Which types of templates should be transformed?
```

## markdown

* https://www.11ty.dev/docs/languages/markdown/
* https://github.com/markdown-it/markdown-it#init-with-presets-and-options

## addFilter()

```js
  eleventyConfig.addFilter( "myFilter", function(value) {
    return value;
  });
```

## setDataDeepMerge()

```js
  /// Opts in to a full deep merge when combining the Data Cascade
  module.exports = function(eleventyConfig) {
  eleventyConfig.setDataDeepMerge(true);
};
```

## addCollection()

```js
module.exports = function (eleventyConfig) {
  // generatre a collection from a folder
  eleventyConfig.addCollection("blogposts", function (collection) {
    return collection.getFilteredByGlob("./src/blogposts/*.md").reverse();
  });
};
```
## addPassthroughCopy()

* relative to the root of your project and not your Eleventy input directory

```js
// Copy `img/` to `_site/subfolder/img`
eleventyConfig.addPassthroughCopy({ "img": "subfolder/img" })
// Copy `src/img/` to `_site/subfolder/img`
eleventyConfig.addPassthroughCopy({ "src/img": "subfolder/img" })

eleventyConfig.addPassthroughCopy("*.{css,js}")
eleventyConfig.addPassthroughCopy({ "_input/assets/styles": "assets/styles/page" })
eleventyConfig.addPassthroughCopy({ "_input/assets/fonts": "assets/fonts" })
eleventyConfig.addPassthroughCopy({ "_components/*.css": "assets/styles" }) // copy component styles to assets/styles
```

## syntax highlighting

`npm install @11ty/eleventy-plugin-syntaxhighlight --save-dev`
Open up your Eleventy config file (**.eleventy.js**) and use `addPlugin`:

```js
  const syntaxHighlight = require("@11ty/eleventy-plugin-syntaxhighlight");
  module.exports = function(eleventyConfig) {
    eleventyConfig.addPlugin(syntaxHighlight);
  };
```

# BACKUP

```js
function renderFsStructure(allCollection) {
	let fsStructure = {
		root: []
	}

	for (const doc of allCollection) {
		let parts = doc.filePathStem.split('/')
		parts = parts.slice(1, parts.length) // slice off '' // ['cult', 'lyrics', 'about-me']
		if (parts.length === 1) {
			fsStructure.root.push(parts[0])
		}
		else if (parts.length >= 2) {
			let pointer = fsStructure
			for (let i = 0; i < parts.length; i++) {
				const part = parts[i]
				if (!pointer[part]) {
					if (i <= parts.length - 3) {
						pointer[part] = {}
					}
					else if (i == parts.length - 2) { // vorletztes
						pointer[part] ??= {}
						pointer[part].root = []
					}
					else if (i == parts.length - 1) { // letztes
						pointer.root ??= []
						let url = Buffer.from(doc.url).toString()
						pointer.root.push(url)
					}
				}
				pointer = pointer[part]
			}
		}
		else {
			console.error(parts)
		}
	}

	let list = renderHTML(fsStructure)

	return list

	function renderHTML(obj) {
		if (Array.isArray(obj)) {
			let listItems = obj.map(item => {
				if (item === 'root') return ''
				else {
					return /* html */`<li><a href="${item}">${path.basename(item)}</a></li>`
				}
			}).join('');
			
			return /* html */`${listItems}`
		}
		else if (typeof obj === 'object') {
			return Object.entries(obj).map(([key, value]) => {
				return /* html */`
					<ul>
						${(key !== 'root') ? /* html */`<li class="dir">${key}</li>` : ''}
						${renderHTML(value)}
					</ul>`;
			}).join('');
		}
		return '';
	}
}
```



# NOT USED ANY MORE

```js
renderArray(data) {

		/* This render function was used when the data was retrieved by fetch() */

		return `
			<un-page-colors data-hsl="2.6, 92.1%, 24.9%" data-grades="7, 14, 21"></un-page-colors>
			${data.pagination.items.map(page => {
				console.log('---')
				console.log('rendering page: ', page.title)
				const pageHeader = data.headers.docs.find(header => header.id === page.header)
				if (pageHeader) console.log('\tpageHeader found!')
				const pageMenu = data.menus.docs.find(menu => menu.id === page.menu)
				if (pageMenu) console.log('\tpageMenu found!')

				const headerHTML = (pageHeader)
					? `<header>
						${renderHTMLFromBlocks({
							blocks: pageHeader.blocks,
							theme: data.site.user,
							site: data.site,
					})}
						</header>`
					: ''

				const navHTML = (pageMenu)
					? `<nav>
						${renderHTMLFromBlocks({
							blocks: pageMenu.blocks,
							theme: data.site.user,
							site: data.site,
					})}
						</nav>`
					: ''

				//console.log('page.blocks: ', page.blocks)
				return `
					${headerHTML}
					${navHTML}
					<main data-page=${page.slug}>
						${renderHTMLFromBlocks({
							blocks: page.blocks,
							posts: data.posts.docs,
							theme: data.site.user,
							site: data.site,
					})}
					</main>
				`;
			}).join("")}
		`
	}
```

