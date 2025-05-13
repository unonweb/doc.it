# NOTES

dotenv        8.6.0   8.6.0  16.4.5  node_modules/dotenv      cms
ts-node       9.1.1   9.1.1  10.9.2  node_modules/ts-node     cms
typescript    4.9.5   4.9.5   5.4.5  node_modules/typescript  cm

## issues

### dev db

* admin collection is overwritten
* @font-face must be regenerated

- [ ] exclude admin collection from restore

## custom elements

* [build a lightbox with the dialog element](https://polypane.app/blog/building-a-lightbox-with-the-dialog-element/)

* mapping between payload options and element attributes:
  * better than static relation because this way I can change/modify the options to fit the element's requirements

* **data-attributes** 
  * are useful if we want to set values in JS that shall be available in CSS, too
  * I can also just use attributes, but then I have to access them imperatively everytime I need them in JS

* custom-element as manager
  * state 

### toggleController

* If I want to **init with state = off** then the button needs to be visible!

* host
  * receives attributes from outside
  * creates and configures controller
  * connects controller with destination and trigger
* controller
  * eventually creates trigger
  * adds attributes to destination and trigger
  * adds classes to destination and trigger

## mechanisms

```js
// after save always set values to context.sites.HTMLHead
context.sites.HTMLHead['site.colors.primary'] = value
```

## payload

### architecture

* standalone dropdown
  * options grouped

### payload 3.0 migration

import issues:

* esbuild: ./src/helpers/bundlePaths.js
* sites: color-picker-field

# TESTS

- [x] test create new page
- [ ] test create new site

# To do

- [x] convert all page editingModes to lexical and then hide these options
- [x] turn into async: **iterateBlocks** and **renderLexicalHTML**
- [x] remove **populateContext with documents**
- [x] remove **populateContext with posts**
- [x] **populateContext with pages**: currently necessary because of:
  * renderHTMLAltLocaleURL
  * renderLinkInternal
- [x] **HTML.body.render**: change URL to cms.unonweb.de:3000
- [x] build automatic creating and assignment of docs in HTML collection when a new page is created
- [ ] **page URLS** create dedicated collection
- [x] **customElement modules **
  - [x] **imports** will not work any more if those files are bundled
  - [x] **exclude** some modules **from bundles**
- [ ] <u>imgs</u>: **lazy loading** = default, add options for all img blocks
- [ ] finish: **Assets.html.head.renderProd**
- [ ] make sure that during dev there are no changes commited to HTML collection that will break prod (!!)
- [ ] implement **fallbackLocale** into site so user may choose
- [ ] check if children block fields of **drop-down render** their html before drop-down renders
- [ ] make sure that both posts and pages are saved to disk in all locales available

## sites

- [ ] **Admin**: fetch **css.variables** and **css.classes** from admin (updates from Admin have been disabled!)
- [ ] enable creation of **multiple footers / navs** (but only one may be selected)
  - [ ] header has to changed to allow multiple, too
- [ ] improve workflow when page title is changed

## posts

- [ ] **createWebVersion**: posts must have the same shape no matter if fetched from payload or from filesystem!
- [ ] implement **pagination** in frontend
- [ ] **saveToDisk** only on publish
- [ ] **hasOwnPage**: implement 

## pages

- [ ] Improve **performance** when updating multiple pages with multiple locale versions. 
  Now each locale is called independently. No reuse of information.

## images

- [ ] **dead references**:
  what happens when img is deleted but still referenced somewhere?

## environment

- [ ] use **importmaps**:

  benefits

  * update packages like Lit in one place without the need to adapt every import of the package
    (alternatively I could save the current version as 'lit' without any version reference)

  * enable imports like

    ```js
      import toUpper from 'lodash/toUpper.js';
    ```

  issues:

  * currently Safari until Version 16.3 does not support it
  * Lit has apparently no easy way to import single modules from node_modules

- [ ] add **html linter**

  to warn if something's wrong...

- [ ] add Elix Components and other libraries as a themes

## blocks

- [ ] **un-img options**

  a) fixed width & height

  b) fixed width & aspect-ratio

  c) fixed height & aspect-ratio

  d) fixed width & auto (intrinsic height?)

  e) fixed height & auto (intrinsic width?)

# FEATURES

## Users

### Pages

- [ ] **enableRichTextRelationship**

### Posts

- [ ] **enableRichTextLink**

  Make it possible to link to individual posts from rich-text.

  Issues:

  * Posts are not necessarily created as html
  * If all posts show up in internal link list dialog this will be overwhelming

- [ ] **enableRichTextRelationship**

### Images

- [ ] permanently filter images uploaded by unonweb