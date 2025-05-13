# ACCESSIBILITY

You should expect a well-designed reader to tell you all, or at least  most, of the following information about the elements it encounters.

- The element's *role* or type, if it is specified (it should be).
- The element's *name*, if it has one (it should).
- The element's *value*, if it has one (it may or may not).
- The element's *state*, e.g., whether it is enabled or disabled (if applicable).

Accessible Rich Internet Applications (ARIA)

```html
<!-- Roles define what an element is or does on the page or app -->
<div role="button">Self-destruct</div>

<!-- Properties express characteristics or relationships to an object. -->
<div role="button" aria-describedby="more-info">Self-destruct</div>
<div id="more-info">This page will self-destruct in 10 seconds.</div>

<!-- States/values define the current conditions or data values associated with the element. -->
<div role="button" aria-describedby="more-info" aria-pressed="false">Self-destruct</div>
```

## rules

1. **Don't use ARIA**

2. **Don't add (unnecessary) ARIA to HTML**

3. **Always support keyboard navigation**

   All interactive (not disabled) ARIA controls must be keyboard accessible. You can add `tabindex= "0"` to any element that needs a focus that doesn't normally receive keyboard focus.

```html
<span role="button" tabindex="0">Submit</span>
```

4. **Don't hide focusable elements**

   Don't add `role= "presentation"` or `aria-hidden= "true"` to elements that need to have focus—including elements with a `tabindex= "0"`. When you add these roles/states to elements, it sends a message to the  AT that these elements are not important and to skip over them. This can lead to confusion or disrupt users attempting to interact with an  element.

5. **Use accessible names for interactive elements**

   The purpose of an interactive element needs to be conveyed to a user before they know how to interact with it. Ensure that all elements have an accessible name for people using AT devices.

   Accessible names can be 

   * the content surrounded by an element (in the case of an `<a>`)

   * alternative text

   * a label

For each of the following code samples, the accessible name is "Red leather boots."

```html
<!-- A plain link with text between the link tags. -->
<a href="shoes.html">Red leather boots</a>

<!-- A linked image, where the image has alt text. -->
<a href="shoes.html"><img src="shoes.png" alt="Red leather boots"></a>

<!-- A checkbox input with a label. -->
<input type="checkbox" id="shoes">
<label for="shoes">Red leather boots</label>
```

## aria-label

### accessible naming and describing techniques:

- naming with child content
- naming with `aria-label`
- naming by referencing content with `aria-labelledby`.
- naming form controls with the `label` element.
- naming fieldsets with the `legend` element.
- naming tables and figures with captions.
- fallback names derived from titles and placeholders.

describing

- describing by referencing content with `aria-describedby`.
- describing tables and figures with captions.
- descriptions derived from titles.

### naming with child content

```html
<a href="/">Home</a>
```

Attention: If an element that supports naming from child content is named by using `aria-label` or `aria-labelledby`, content contained in the element and its descendants is hidden from assistive technology users unless the descendant content is referenced  by `aria-labelledby`.

When a button contains text, that text is going to be used to create an accessible name for the button. That text should usually be enough to convey what the button does to screen readers.

As such, the icon itself (an `svg` image) could become irrelevant to screen reader users, as it does not clarify or add to the  accessible name of the button. As such, the common practice is to hide  it from screen readers using `aria-hidden`.

### naming with aria-label

* an interactive element *without inner text* should have an `aria-label`
* a button that has an icon instead of text, like a hamburger menu, should have `aria-label="menu"`
* When applied to an element that supports *naming from child content*, `aria-label` hides descendant content from assistive technology users and replaces it with its value
* don't use an `aria-label` that is different from the visual text label!
* set by html attribute not by javascript! `el.ariaLabel = "menu"` is not supported in Firefox!

```html
<button>Next</div> <!-- will be announced as “Next” -->
<button aria-label="go to next page">Next</button> <!-- will be announced as “Go to next page” -->
<button type="button" aria-label="Close">X</button>
```

## aria-hidden

`aria-hidden="true"`

* can be used to hide non-interactive content from the accessibility API
* removes that element and all of its children from the accessibility  tree. This can improve the experience for assistive technology users by hiding:
  * Purely decorative content, such as icons or images
  * Duplicated content, such as repeated text
  * Offscreen or collapsed content, such as menus

```html
<span aria-hidden>×</span>
<span class="sr-only">Close dialog</span>
```

The close button contains two spans: one that has a "×" character and is hidden from screen readers, and one that says "Close dialog" and is  hidden from sighted users. This way we can show a close icon for sighted users without having screenreaders read "times" out loud.

## role

#### landmark roles

* Do not use `role="landmark"`. Do use HTML and subclass landmark roles.

- header: `role="banner"`
- complementary
- footer:  `role="contentinfo"`
- form
- main: `role="main"
- nav: `role="navigation"
- region
- search

## aria-haspopup

**Note:** Make sure the role of the element that serves as the container for the popup content is a `menu`, `listbox`, `tree`, `grid` or `dialog` and that the value of `aria-haspopup` matches the role of the popup container.

BASICS
======================


Inline vs Block
-----------------------------

The distinction of block-level vs. inline elements was used in HTML specifications up to 4.01.  
In HTML5, this binary distinction is replaced with a more complex set of *content categories*.

* *block-level* is not technically defined for elements that are new in HTML5.

block elements

* always start on a new line and take up the full width available (stretches out to the left and right as far as it can).
* may appear only within a `<body>` element.
* may contain inline elements and (sometimes) other block-level elements

inline elements 

* do not start on a new line and only takes up as much width as necessary.

ASSETS
======================

## CSS

Three ways to include CSS in html:

* **external**
* **internal** (will overwrite *external*)
* **inline** (will overwrite *internal* and *external*)

```html
<!-- External CSS -->
<link rel='stylesheet' href='style.css' type='text/css' />

<!-- Internal CSS -->
<!-- include page-specific CSS directly in the head of the document. -->
<style>
	body:before {
		content: "I have some valid use cases, that Leland will discuss later in the article. :)";
	}
</style>

<!-- Inline CSS -->
<!-- apply CSS directly to the HTML element. -->
<div style="font-size: 300%; color: red">
	This CSS can't be overridden without a hacky !important flag.
</div
```

### external

CSS is treated as a **render-blocking resource** — that is, if you include any form of `<link href="style.css" rel="stylesheet">` in the document’s `<head>`, the browser will trigger an additional request to the server to retrieve that stylesheet before even beginning to render your page for the user.

<u>advantages</u>:

* Can be cached by the browser. This means once the style sheet is loaded, it does not need to be loaded again for any other pages that apply the same stylesheet, increasing the overall loading speed for subsequent pages

### internal

<u>disadvantages</u>

* `<link>` in the head of the document blocks the rendering of HTML. 
  Without Critical CSS and with an empty cache, you will block the first paint, until your CSS bundle gets loaded.

### inline

Inlining in general is the practice of integrating a portion of code directly into the place where it will be used, eliminating the need for the computer to do a function call or some other type of lookup. This makes the code faster.

<u>advantages</u>:

* makes the initial loading faster

<u>disadvantages</u>:

- disables caching (the browser will need to read and apply CSS every time a page is loaded)
- no way to manipulate pseudoclasses.
- Need an `!important` flag to override.
- If an !important flag is used inline, you need JavaScript to override

### conclusion

- use inline CSS if your CSS rules are few and your project is not extensively large
  'cause it reduces HTTP requests
- use external CSS for larger projects with a number of webpages. 
  utilizing external stylesheets enables you to style multiple pages with a single file, making site maintenance easier and more efficient.

### critical vs non-critical CSS

All the CSS files that you place into the `<head>` of your HTML page are automatically treated as render-blocking resources. However, you don’t need all of this code to render the critical part of your page: above-the-fold content. Splitting CSS into critical and non-critical parts is a performance optimization technique that has gained a lot of popularity since the introduction of *Core Web Vitals*, as it also improves LCP scores (rendering time of the largest content element above the fold).

#### critical

Either add the critical CSS file as an external resource to the `<head>` or inline it using the `<style>` tag to also reduce the number of HTTP requests.

#### non-critical

Move it down before the closing` <body>` tag so that it will become a non-render-blocking resource

### defer css

```html
<link rel="preload" href="styles.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
<noscript><link rel="stylesheet" href="styles.css"></noscript>
```

- link **rel="preload" as="style"** requests the stylesheet asynchronously.
- The **onload** attribute in the link allows the CSS to be processed when it finishes loading.
- "nulling" the onload handler once it is used helps some browsers avoid re-calling the handler upon switching the rel attribute.
- The reference to the stylesheet inside of a **noscript** element works as a fallback for browsers that don't execute JavaScript.

SEMANTICS
========================================

* `<details>`     # Defines additional details that the user can view or hide
* `<summary>`     # Defines a visible heading for a `<details>` element

## meta elements

Elements belonging to the metadata content category modify the presentation or the behavior of the rest of the document, set up links to other documents, or convey other out-of-band information.

### `<base>`

### `<link>`

### `<meta>`

### `<noscript>`

### `<script>`

### `<style>`

### `<title>`

## sectioning elements

Each section (body, section, article, aside, nav) can have their own `<header> ... </header>` and `<footer> ... </footer>` tags, that defines the header (such as logo, author’s name, dates, meta info, etc.) and the footer (copyright, notes, links, etc.) of that  section.

### Labeling section content

```html
<header>
  <nav aria-labelledby="primary-navigation">
    <h2 id="primary-navigation">Primary navigation</h2>
    <!-- navigation items -->
  </nav>
</header>

<!-- page content -->

<footer>
  <nav aria-labelledby="footer-navigation">
    <h2 id="footer-navigation">Footer navigation</h2>
    <!-- navigation items -->
  </nav>
</footer>
```

### `<aside>`

Can be used for typographical effects like pull quotes or sidebars, for advertising, for groups of nav elements, and for other content that is considered separate from the main content of the page.

* defines content aside from the content it is placed in 
* should be indirectly related to the surrounding content
* often represented as sidebars in printed typography

### `<article>`

* independent, self-contained content
* should make sense on its own
* should be possible to distribute it independently from the rest of the web site
* should be identified, typically by including a **heading** element as a child
* Author information can be provided through the `<address>` element
* The publication date and time can be described using the `datetime` attribute of a `<time>` element.
* Blog post, Newspaper article

### `<footer>` 

### `<header>`

may contain some heading elements but also a logo, a search form, an author name, and other elements.

```html
<article>
  <header>
    <h1>Beagles</h1>
    <time>08.12.2014</time>
  </header>
  <p>I love beagles <em>so</em> much! Like, really, a lot. They’re adorable and their ears are so, so snuggly soft!</p>
</article>
```

### `<main>` 

### `<nav>`

intended only for a **major block of navigation links**

Not all groups of links on a page need to be in a `nav` element —  the element is primarily intended for sections that consist of major navigation blocks. In  particular, it is common for footers to have a short list of links to various pages of a site,  such as the terms of service, the home page, and a copyright page. The `footer` element  alone is sufficient for such cases; while a `nav` element can be used in such cases, it  is usually unnecessary.

As such, the `nav` element is only really for things like 

* **primary navigation**: navigating between pages on your site
* **secondary navigation**: navigating between sections on the current page

```html
<nav>
  <ul>
    <li><a href="#home">Home</a></li>
    <li><a href="#about">About Us</a></li> 
    <li><a href="#contact">Contact Us</a></li> 
  </ul>
</nav>
```

### dropdown menu

```html
<nav>
   <select name="menu">
      <option value="home">Home</option> 
      <option value="about">About Us </option> 
      <option value="contact">Contact Us </option> 
   </select>   
 </nav>
```

### `<section>`

* sections grouped around a specific theme
* a thematic grouping of content, typically with a heading
* each `<section>` ***should be identified, typically by including a heading*** as a child, wherever possible
* defines a `region` landmark when it has an accessible name (`aria-labelledby`, `aria-label` or `title`)

```html
<section>
  <h2>Heading</h2>
  <p>Bunch of awesome content</p>
</section>
```

#### headings and sections

Authors are also encouraged to explicitly wrap sections in elements of sectioning content, instead of relying on the implicit sections generated by having multiple headings in one element of sectioning content.

```html
<!-- For example, the following is correct: -->
<body>
 <h4>Apples</h4>
 <p>Apples are fruit.</p>
 <section>
  <h2>Taste</h2>
  <p>They taste lovely.</p>
  <h6>Sweet</h6>
  <p>Red apples are sweeter than green ones.</p>
  <h1>Color</h1>
  <p>Apples come in various colors.</p>
 </section>
</body>
```

However, the same document would be more clearly expressed as:

```html
<body>
 <h1>Apples</h1>
 <p>Apples are fruit.</p>
 <section>
  <h2>Taste</h2>
  <p>They taste lovely.</p>
  <section>
   <h3>Sweet</h3>
   <p>Red apples are sweeter than green ones.</p>
  </section>
 </section>
 <section>
  <h2>Color</h2>
  <p>Apples come in various colors.</p>
 </section>
</body>
```

This third example is also semantically identical, and might be easier to maintain (e.g. if sections are often moved around in editing):

This final example would need explicit style rules to be rendered well in legacy browsers. Legacy browsers without CSS support would render all the headings as top-level headings.

```html
<body>
 <h1>Apples</h1>
 <p>Apples are fruit.</p>
 <section>
  <h1>Taste</h1>
  <p>They taste lovely.</p>
  <section>
   <h1>Sweet</h1>
   <p>Red apples are sweeter than green ones.</p>
  </section>
 </section>
 <section>
  <h1>Color</h1>
  <p>Apples come in various colors.</p>
 </section>
</body>
```

```html
<article>
	<section></section>	
  <section></section>	
  <section></section>	
</article>
<!-- nesting sections inside an article of content is logical for a machine, and also for a human (code will be easier to read for the developer).-->
```

## other

### `<menu>`

* Implicit ARIA role: list
* Permitted content:  <li>, <script>, <template>


```html
<menu>
  <li><button onclick="copy()">Copy</button></li>
  <li><button onclick="cut()">Cut</button></li>
  <li><button onclick="paste()">Paste</button></li>
</menu>
```

### inline text

### `<a>`

* implicit aria role: **link** when href attribute is present

### `<cite>`

* mark up the title of a cited creative work
* The reference may be in an abbreviated form according to context-appropriate conventions related to citation metadata.

### `<code>`

### `<mark>`



### `<span>`

### `<time>`

```html
<time>2011-11</time> <!-- this text happens to be a valid datetime attribute value -->
<time datetime="2011-11">A cold winter month many years ago</time>
<time datetime="2011-11">November, 2011</time>
```

### text formatting

Formatting elements were designed to display special types of text:

`<b>` - Bold text
`<strong>` - Important text
`<i>` - Italic text
`<em>` - Emphasized text
`<mark>` - Marked text
`<small>` - Smaller text
`<del>` - Deleted text
`<ins>` - Inserted text
`<sub>` - Subscript text
`<sup>` - Superscript text

`<sub>`

`<sub>` # Defines subscript text. Subscript text appears half a character below the normal line, and is sometimes rendered in a smaller font.
`<sup>` # Defines superscript text. Superscript text appears half a character above the normal line, and is sometimes rendered in a smaller font.

`<i>`

* defines a part of text in an alternate voice or mood. The content inside is typically displayed in *italic*
* is often used to indicate a *technical term*, a *phrase from another language*, a *thought*, a ship name, etc.
* use it only when there is not a more appropriate semantic element, such as:

* `<em>` (emphasized text)
* `<strong>` (important text)
* `<mark>` (marked/highlighted text)
* `<cite>` (the title of a work)
* `<dfn>` (a definition term)

Content categories
=========================================

## Flow content

These elements typically contain text or embedded content.  

### Sectioning content

These elements create a section in the current outline that defines the scope of `<header>` elements, `<footer>` elements, and heading content.  

```html
<!-- Elements belonging to this category are -->
<article>
	self-contained piece of content
  could theoretically be distributed to other websites as a stand-alone unit
  blog posts, news articles
</article>
<aside>
	represents a portion of a document whose content is only indirectly related to the document's main content
  sidebar, call-out boxes
</aside>
<nav></nav>
<section></section>
```

### Heading content

These elements define the title of a section, whether marked by an explicit sectioning content element, or implicitly defined by the heading content itself.  

```html
<!-- Elements belonging to this category are -->
<h1></h1>
<h2></h2>
<h3></h3>
<h4></h4>
<h5></h5>
<h6></h6>
```

### Phrasing content

These elements define the text and the mark-up it contains. Runs of phrasing content make up paragraphs.

```html
<!-- Elements belonging to this category are -->
<span></span>
<i></i> <!-- idiomatic text: technical terms, taxonomical designations -->
<abr></abr>
<picture></picture>
<iframe></iframe>
<img>
<input>
<button></button>
<label></label>
<output></output>
<sub></sub>
<sup></sup>
<textarea></textarea>
```

### Embedded content

* imports another resource or inserts content from another mark-up language or namespace into the document.

```html
<!-- Elements belonging to this category are -->
<audio></audio>
<video></video>
<canvas></canvas>
<svg></svg>
<iframe></iframe>
<picture></picture>
<img>
<embed>
<math></math>
<object></object>
```

### Interactive content

```html
<!-- Elements belonging to this category are -->
<a></a>
<button></button>
<details></details>
<embed>
<iframe></iframe>
<label></label>
<select></select>
<textarea></textarea>

<!-- Elements that belong to this category under specific conditions: -->

<audio></audio> <!-- if 'controls' attribute is present -->
<video> <!-- if 'controls' attribute is present -->
<img> <!-- if 'usemap' attribute is present -->
<input> <!-- if 'type' attribute is not in the hidden state -->
<menu> <!-- if 'type' attribute is in the toolbar state -->
<object> <!-- if 'usemap' attribute is present -->
```

### Form-associated content

comprises elements that have a form owner, exposed by a form attribute.  
A form owner is either the containing `<form>` element or the element whose id is specified in the `form=""` attribute.
Elements that belong to this category:

```html
<!-- Elements belonging to this category are -->
<button></button>
<fieldset></fieldset>
<input>
<label></label>
<meter></meter>
<output></output>
<progress></progress>
<select></select>
<textarea></textarea>
```

# HTML tags

## head

### `<meta>`

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0"> 
<!-- sets the viewport of your page, which will give the browser instructions on how to control the page's dimensions and scaling -->

<meta http-equiv="refresh" content="30"> 
<!-- refresh page constantly every 30 seconds -->
```

## `<title>`

* length should be between 30 and 60 characters

## `<a>`

#### href

* A linked page is normally displayed in the current browser window, unless you specify another target.

```html
<!-- absolute: -->
<a href="https://www.example.com/default.htm"></a> 	

<!-- relative: -->
<a href="//yourdomain.example/images/example.png"></a>
This URL is relative based on the current scheme used and should almost always be used when including external resources (images, javascripts etc).

<a href="/images/example.png"></a>
For local resources this is the prefered way of referencing them. This is a relative URL based on the document root of your website.

<a href="images/example.png"></a>
This is also a relative URL although a bit different than the previous one. This URL is relative to the current path. What this means is that it will resolve to different paths depending on where you are in the site.
```

* don't use absolute URLs for local resources (including scheme (e.g. HTTP / HTTPS) and the hostname (e.g. yourdomain.example)) because it will be terrible to maintain and debug.
* always use relative URLs (for local resources).

#### download

```html
<!-- specifies that the target will be downloaded when a user clicks on the hyperlink -->
<a download="pathtofile"></a>
```

#### href=""#dest"

```html
<!-- link to an element with a specified id within the page: -->
<a href="#section2"></a>
<!-- link to the top of the current page: -->
<a href="#"></a> <!-- or --> 
<a href="#top"></a>
```

#### href="protocol:"

```html
<!-- a script: -->
<a href="javascript:alert('Hello');"></a>
<!-- other protocols like ftp://, mailto:, file: are also possible -->
```

#### rel

```html
<!-- relationship between the current document and the linked document -->
<a rel="alternate"></a>
<a rel="author"></a>
<a rel="bookmark"></a>
<a rel="external"></a>
<a rel="help"></a>
<a rel="license"></a>
<a rel="next"></a>
<a rel="nofollow"></a>
<a rel="noreferrer"></a>
<a rel="noopener"></a>
<a rel="prev"></a>
<a rel="search"></a>
<a rel="tag"></a>
```

#### target

```html
<!-- where to open the linked document -->
<a target="_blank"></a> <!-- open link in a new tab -->
<a target="_parent"></a>
<a target="_self"></a>
<a target="_top"></a>
<a target="framename"></a>
```

#### referrerpolicy

```html
<a referrerpolicy="no-referrer"></a>
<a referrerpolicy="no-referrer-when-downgrade"></a>
<a referrerpolicy="origin"></a>
<a referrerpolicy="origin-when-cross-origin"></a>
<a referrerpolicy="unsafe-url"></a>
```

#### hreflang

```html
<!-- specifies the language of the linked document: -->
<a hreflang="language_code"></a>
```

#### other

```html
<!-- specifies what media/device the linked document is optimized for: -->
<a media="media_query"></a>
<!-- list of URLs to which, when the link is followed, post requests with the body ping will be sent by the browser (in the background). Typically used for tracking. -->
<a ping="url1 url2"></a> 

<!-- media type of the linked document -->
<a type="mediatype"></a>
```

## `<abbr>`

* Flow content, phrasing content, palpable content 

```html
<p>
  The <abbr title="World Health Organization">WHO</abbr> was founded in 1948.
</p>
```

## `<address>`

* should include the name of the person, people, or organization to which the contact information refers
* Typically can be placed inside the `<footer>` element of the current section, if any.

```html
<address>
  <a href="mailto:jim@example.com">jim@example.com</a><br />
  <a href="tel:+14155550132">+1 (415) 555‑0132</a>
</address>
```

## `<article>`

* independent, self-contained content
* should make sense on its own
* should be possible to distribute it independently from the rest of the web site
* should be identified, typically by including a **heading** element as a child
* Author information can be provided through the `<address>` element
* The publication date and time can be described using the `datetime` attribute of a `<time>` element.
* Blog post, Newspaper article

### article header

```html
<article>
  <header>
    <h2>The Planet Earth</h2>
    <p>
      Posted on Wednesday, <time datetime="2017-10-04">4 October 2017</time> by
      Jane Smith
    </p>
  </header>
  <p>
    We live on a planet that's blue and green, with so many things still unseen.
  </p>
  <p><a href="https://example.com/the-planet-earth/">Continue reading…</a></p>
</article>
```

`<button>`
-------------------------

### accessibility

Buttons that only show an icon to represent do not have an accessible name. If you want to visually hide the button's text, an accessible way to do so is to use a combination of CSS properties to remove it visually from the screen, but keep it parsable by assistive technology.

### button vs. input

Inside a `<button>` element you can put content, like text or images. 
This is the difference between this element and buttons created with the `<input>` element.  
`<button>` elements are much easier to style than `<input>` elements. You can add inner HTML content (`<i>`, `<br>`, or even `<img>`), and use `::after` and `::before` pseudo-elements for complex rendering.

### button & forms

If you use the `<button>` element in an HTML form, different browsers may submit different values. 
Use `<input>` to create buttons in an HTML form.
If your buttons are not for submitting form data to a server, be sure to set `type="button"`.  
Otherwise they will try to submit form data and to load the (nonexistent) response.

### attributes

```html
<button formaction="">The URL that processes the information submitted by the button. Overrides the action attribute of the button's form owner. Does nothing if there is no form owner.</button>
```

#### type

```html
<button type="submit"></button>
<!-- This button submits the form data to the server. This is the default if the attribute is not specified for buttons associated with a `<form>`, or if the attribute is an empty or invalid value. -->
<button type="reset"></button>
<!-- This button resets all the controls to their initial values, like <input type="reset">. (This behavior tends to annoy users.) -->
<button type="button"></button> 
<!-- This button  has no default behavior, and does nothing when pressed by default. It can have client-side scripts listen to the element's events, which are triggered when the events occur. -->
```

## `<blockquote>` | `<cite>`

```html
<blockquote>
  <p>Wisdom is the reward you get for a lifetime of listening when you'd have preferred to talk.</p>
  <cite>― Doug Larson</cite>
</blockquote>
```

```html
<q>
  The greatest glory in living lies not in never falling but rising every time we fall.
</q>
<cite>― Nelson Mandela</cite>
```

## `<dfn>`

```html
<p>
  In web development, a <dfn title="Hypertext Markup Language">HTML</dfn> file consists of structured elements for building web pages.
</p>
```

## `<dialog>` and popover

* https://hidde.blog/dialog-modal-popover-differences/

### terminology

* modal: Basically, when a modal component is open, **it is the only thing that is *not* inert**
* With **light dismiss**, a component disappears automatically on certain  conditions, like when users scroll, interact with something else or  click outside of the component. If you need **light dismiss** use `popover` attribute.

### dialog

* designed for components that require user interaction
* In a **modal dialog**, users need to handle the dialog first before they can continue using the app. This means they can’t do anything else in the  app until they close or cancel the dialog.
  * newsletter subscription prompt
* **Non-modal dialogs** allow users to keep using the app even when the dialog is open
  * cookie consent

#### forms

`<form>` elements can be used to close a dialog box if they have the attribute `method="dialog"` or if the button used to submit the form has `formmethod="dialog"` set. When a `<form>` within a `<dialog>` is submitted via the `dialog` method, the dialog box closes, the states of the form controls are saved but not submitted, and the `returnValue` property gets set to the value of the button that was activated.

#### accesibility

Dialogs must have an accessible name. Associate your dialog with the visible heading or message (if brief) using `aria-labelledby` on the `<dialog>`. You could also use `aria-label`, but associating with visible text is ideal, because it creates parity between what folks see and what assistive tech call stuff.

### dialog vs popover

Ddialogs with non-modal behavior are frequently categorized as popovers. This is where the distinction between dialogs and popovers starts to  blur, making it difficult for developers to choose between the two.

* dialog elements are suitable for presenting users with important  action-related information
* for less crucial scenarios, we now  have the newly introduced Popover API

### popover

* Popovers are **not modal**

* Popovers can have **light dismiss** behaviour, meaning they close by themselves (`popover="auto"`)

* Popovers can be opened, closed and toggled without JS:

  With a `<button>` in HTML and the `popovertarget` attribute that points to the popover's ID, the browser can take care of showing, hiding and toggling.

* Popovers can have **focus trapped** in them, for instance in complex widgets where you want to avoid that people accidentally tab out of the widget. A focus trap does not make a popover modal, as users can still access  everything else on the page, it is just something that can improve  usability in certain cases.

examples:

- Displaying tooltips or help text
- Opening action or context menus featuring a range of choices
- Initiating non-modal dialogs for actions like toast notifications, hover cards, fly-outs, etc.
- Providing context-related information during application tours or onboarding processes

```html
<button type="button" popovertarget="datepicker">Pick date</button>
<dialog popover id="datepicker"></dialog>
```

In this case, the dialog is turned into a popover with the `popover` attribute, which adds the popover behaviours. The button will toggle  the popover, because the popover's ID matches the button's `popovertarget` attribute.

The button can also be set op to just show or just hide, in this case use `popovertargetaction` with `show` or `hide` (the `toggle` value exists too, and is the default).

To open the popover when the page loads, set `defaultopen` on the popover. This is useful for teaching UI.

To move focus to a popover when it opens, set the `autofocus` attribute on the popover itself, or an element within it. Normally,  this attribute sets focus on page load. But if it is used on or within  popovers, it only sets focus when the popover is shown (this can be on  page load if `defaultopen` is used).



## `<dl>` | `<dt>` |`<dd>`

-  **`<dl>`** specifies a description list
- **`<dt>`** specifies a term in a description or definition list
- **`<dd>`** provides the description, definition, or value for the preceding term (`<dt>`) in a description list (`<dl>`)

```html
<p>Cryptids of Cornwall:</p>
<dl>
    <dt>Beast of Bodmin</dt>
    <dd>A large feline inhabiting Bodmin Moor.</dd>

    <dt>Morgawr</dt>
    <dd>A sea serpent.</dd>

    <dt>Owlman</dt>
    <dd>A giant owl-like creature.</dd>
</dl>
```

## `<details>` | `<summary>`

* use `<details>` to create an accordion-like widget that the user can toggle open and closed
* `<details>` is the wrapper for all the content we want to show and hide
* `<details open>` # by default the content is hidden when the page loads. We can make it visible by default by adding the boolean `open` attribute to the `<details>` element
* `<summary>` # specifies a summary, caption, or legend for a `<details>` element's disclosure box. Clicking the `<summary>` element toggles the state of the parent `<details>` element open and closed.
* Works great for FAQs.

```html
<details>
	<summary><label for="name">Name:</label></summary>
	<input type="text" id="name" name="name" />
</details>
```

<details>
	<summary>What is the population of New Orleans?</summary>
	According to 2010 Census Bureau estimates, New Orleans' population is made up of approximately 343,829 residents.
</details>

<details>
	<summary>How do I get to New Orleans?</summary>
	Use Google Maps.
</details>

```html
<details>
  <summary>Click to reveal more details</summary>
  <p>This is the additional content that can be expanded or collapsed.</p>
</details>
```

## `<fieldset>` | `<legend>`

is used to group several controls as well as labels within a web form.

* Because of its influence over assistive technology, `<fieldset>` is one of the key elements for building accessible forms
* screen readers will speak the legend's content before speaking the label of each control

```html
<form>
  <fieldset>
    <legend>Choose your favorite monster</legend>

    <input type="radio" id="kraken" name="monster">
    <label for="kraken">Kraken</label><br/>
    <input type="radio" id="sasquatch" name="monster">
    <label for="sasquatch">Sasquatch</label><br/>
    <input type="radio" id="mothman" name="monster">
    <label for="mothman">Mothman</label>
  </fieldset>
</form>
```

The `<legend>` element represents a caption for the content of its parent `<fieldset>`.

## `<figure>` | `figcaption`

specifies self-contained content, like illustrations, diagrams, photos, code listings, etc

```html
<figure>
	<img src="pic_trulli.jpg" alt="Trulli">
	<figcaption>Fig1. - Trulli, Puglia, Italy.</figcaption>
</figure> 
```

for multiple images:

```html
<figure>
  <img src="image1.png" alt="Image 1" />
  <img src="image2.png" alt="Image 2" />
  <img src="image3.png" alt="Image 3" />
  <figcaption>Three different images, grouped together as a figure, and being captioned with figcaption.</figcaption>
</figure>
```

for code blocks:


```html
<figure>
	<p><code>Life is good, said HTML5 to XHTML.</code></p>
	<figcaption>In this code block, we see HTML5 speaking to XHTML.</figcaption>
</figure>
```

for author attribution

<figure>
  <img src="img/content/preview.jpg" alt="Alttext für das Bild" />
  <footer><small><!-- copyright noice --></small></footer>
  <figcaption>Caption goes here</figcaption>
</figure>

### `<figcaption>`

The first `figcaption` element child of the element, if any, represents the caption of the `figure` element's contents.

## `footer`

> ​    A footer typically contains information about its section such as who wrote it, links to related documents, copyright data, and the like.

* use case: licence

```html
<body>

 <article>
    <footer>
      <small><!-- if the license applies to the content of this article --></small>
    </footer>
  </article>

  <footer>
    <small><!-- if the license applies to the content of the whole page --></small>
  </footer>

</body>
```

`<form>`
----------------------------------------------

used to create an HTML form for user input; can contain one or more of the following form elements:

```html
<form>
	<input>
    <textarea></textarea>
    <button></button>
    <select></select>
    <option></option>
    <optgroup></optgroup>
    <fieldset></fieldset>
    <label></label>
    <output></output>
</form>
```

### attributes

```html
<form
	action="<url>"
	method="get|post"
	name="<text>">
</form>

<form name="<text>"></form>
specifies the name of a form; the form data is sent to the server as name/value pairs

<form action=""></form>
The URL that processes the form submission. This value can be overridden by a 'formaction' attribute on a button or input type="submit" element
<form action="https://example.com">
<form action="about.html"></form>
points to a file within the same web site

<!-- call a function on submit -->

<!-- a) use javascript url form with -->
<form action="javascript:handleClick()"></form>
<!-- b) use onSubmit event handler -->
<form onSubmit="return handleClick()"></form>
If you return false from the handleClick it will prevent the normal submision procedure. 
Return true if you want the browser to follow normal submision procedure.
use evt.preventDefault() to prevent page reload after form submit
    
<form autocomplete="on|off"></form> 
specifies whether a form should have autocomplete on or off
<form method="get|post"></form>
specifies the HTTP method to use when sending form-data
    
<!-- method="post" -->
<form enctype="application/x-www-form-urlencoded"></form>
<form enctype="multipart/form-data"></form>
<form enctype="text/plain"></form>
specifies how the form-data should be encoded when submitting it to the server (only for method="post")
<form target="_blank|_self|_parent|_top"></form>
specifies where to display the response that is received after submitting the form

```

## `<h1>` | `<h2>` | `<h3>`

The heading level corresponds to the levels of nested sections. The h1 element is for a top-level section, h2 for a subsection, h3 for a sub-subsection, and so on.

Prefer using only one `<h1>` per page and nest headings without skipping levels.

## `<header>`

* represents introductory content, typically a group of introductory or navigational aids
* may contain some heading elements  but also a logo, a search form, an author name, and other elements.
* has an identical meaning to the site-wide `banner` landmark role, unless nested within sectioning content. Then, the `<header>` element is not a landmark.
* The HTML header element is not considered a banner landmark when it is descendant of an `<article>`, `<aside>`, `<main>`, `<nav>`, or `<section>` element.

### heading and subtitle

```html
<header>
  <h1>How to bootstrap a product as a solo founder</h1>
  <p class="tagline">
    Important lessongs learned from building 10 products on my own in 2 years
  </p>
</header>
```

## `<hgroup>`

 represents a heading and related content. It groups a single `<h1>`–`<h6>` element with one or more `<p>`

allows the grouping of a heading with any secondary content, such as  subheadings, an alternative title, or tagline. Each of these types of  content represented as a `<p>` element within the `<hgroup>`.

```html
<hgroup id="document-title">
  <h1>HTML: Living Standard</h1>
  <p>Last Updated 12 July 2022</p>
</hgroup>
```

## `<iframe>`

The `<iframe>` (HTML Inline Frame) element represents a *nested browsing context*, embedding another HTML page into the current one.

* Because each browsing context is a complete document environment, every `<iframe>` in a page *requires increased memory* and other computing resources.

#### attributes

* `src` 	# The URL of the page to embed
* `height` 	# height of the frame in CSS pixels. Default is 150.
* `width` 	# width of the frame in CSS pixels. Default is 300
* `referrerpolicy` # Indicates which referrer to send when fetching the frame's resource:
  * `same-origin`: A referrer will be sent for same origin, but cross-origin requests will contain no referrer information.

#### open link in iframe

1. give your iframe a unique name with the `name` attribute. 
2. point your links at that frame using that name as the value of the `target` attribute

```html
<iframe src="example.htm" name="page"></iframe>
<a href="https://www.example.com" target="page">Example</a>
```

There are *four target keywords* that don't require a named frame. These keywords allow you to open links in specific areas of the web browser window that might not have an ID associated with them. These are the targets that web browsers recognize:

* `_self` # The default target for any anchor tag. If you don't set the target attribute or you use this target, the link will open in the same window or frame that the link is in.
* `_parent` # Iframes are embedded inside web pages. You can embed an iframe in a page that is inside another iframe on another web page. When you set the target attribute to _parent, the link will open in the web page that is holding the iframe.
* `_top` # In most situations with iframes, this target will open links in the same way that the _parent target does. But if there is an iframe inside an iframe, the _top target opens links in the highest-level window in the series, removing all the iframes.
* `_blank` # The most commonly used target, this opens the link in an entirely new window, similar to a popup.

`<input>`
--------------------------------------------------

contains attributes only (no text)

### relations to other elements

#### input and form

#### input and name

`name="<name>"` # specifies the name of an input element

* Consider it a required attribute (even though it's not). If an input has no name specified, or name is empty, the input's value is not submitted with the form!
* Without a name, the element won't be identifiable when posting the form back to a server for processing
* HTMLFormElement.elements # When an input element is given a name, that name becomes a property of the owning form element's HTMLFormElement.elements property. 

```js
let form = document.querySelector("form");
// HTMLFormElement.elements
let guestName = form.elements.guest // <input name="guest">
let hatSize = form.elements["hat-size"] // <input name="hat-size">
```

#### input and label

Associate the `<label>` with an `<input>` element
1. give the *input* an `id` attribute
2. give the *label* then a *for* attribute whose value is the same as the input's id

Alternatively, you can nest the `<input>` directly inside the `<label>`, in which case the for and id attributes are not needed because the association is implicit

```html
<!-- style 1 -->
<label for="cheese">Do you like cheese?</label>
<input type="checkbox" name="cheese" id="cheese">

<!-- style 2 -->
<label>Do you like peas?
	<input type="checkbox" name="peas">
</label>
```

Advantages:
* The label text is not only visually associated with its corresponding text input; it is programmatically associated with it too.  
  This means that, for example, a *screen reader* will read out the label when the user is focused on the form input, making it easier for an assistive technology user to understand what data should be entered.
* You can click the associated label to *focus*/activate the input, as well as the input itself.  
  This increased hit area provides an advantage to anyone trying to activate the input, including those using a touch-screen device.
* Even using style 2, it is considered best practice to set the `for` attribute to ensure all assistive technologies understand the relationship between label and widget.

#### input and list

it's common practice to wrap a label and its widget with a `<li>` element within a `<ul>` or `<ol>` list. 
Lists are recommended for structuring multiple checkboxes or radio buttons.


### common attributes

#### misc

```html
<input type="any" value="">
At first, the initial value if specified explicitly in HTML.
More generally, the current value of the form control. Submitted with the form as part of a name/value pair.
<input type="any" autocomplete="on|off">
specifies whether an input element should have autocomplete enabled
<input type="any" disabled>
specifies that an input element should be disabled
<input type="any" autofocus>
specifies that an input element should automatically get focus when the page loads
<input type="any" form="<form-id>">
specifies the form the input element belongs to
```

#### almost all types

```html
<input type="almost-any" required>
specifies that an input field must be filled out before submitting the form
<input type="almost-any" readonly>
# specifies that an input field is read-only
<input type="almost-any" list="<datalist-id>">
refers to a datalist element that contains pre-defined options for an input element
```

#### type

specifies the type to display

#### type="button"

<input type="button">

A push button with no default behavior displaying the value of the value attribute, empty by default.
* the newer `<button>` element is now the favored way to create buttons

#### type="color"

```html
<input type="color">
```

#### type="file"

```html
<input type="file">
<input 
	type="file"
	multiple
	accept="<file_extension>"
	accept="audio/*"
	accept="video/*"
	accept="image/*"
	>	
```

* `multiple="multiple"` # specifies that a user can enter more than one value in an input element
* `accept="<file_extension>"` # specifies a filter for what file types the user can pick from the file input dialog box
* `accept="audio/*"`
* `accept="video/*"`
* `accept="image/*"`
* `accept="media_type"`
* `capture` # defines which media—microphone, video, or camera—should be used to capture a new file for upload with file upload control
  * `capture="user"` # indicates that the user-facing camera and/or microphone should be used
  * `capture="environment"` # specifies that the outward-facing camera and/or microphone should be used
* `files` # A FileList object that lists every selected file. This list has no more than one member unless the multiple attribute is specified.

### type to enter

```html
<input type="password"> <!-- A single-line text field whose value is obscured. Will alert user if site is not secure. -->
<input type="url"> <!-- Looks like a text input, but has validation parameters -->
<input type="email">
<input type="tel">
<input type="search">
<input type="text">

<!-- common attributes -->
<input size="<characters>"> 
specifies the width, in characters, of an input element
<input placeholder="<text>">
specifies a short hint that describes the expected value of an input element
<input maxlength="<number>">
specifies the maximum number of characters allowed in an input element
<input minlength="<number>">
specifies the minimum number of characters required in an input element
<input pattern="<regexp>">
specifies a regular expression that an input element's value is checked against
```

```html
<form action="">
	<label>Message: <input type="text" name="message"></label>
	<button type="submit">Save</button> <span id="feedback"></span>
</form>
```

### click to check

#### type="radio"
* designed for selecting one value out of a set

```html
<input type="radio" checked> <!-- specifies that the element should be pre-selected -->
<input type="radio" checked> 

<input type="radio" name="pizza" value="salami">
<input type="radio" name="pizza" value="4seasons">
<!-- 'name' 
is used to create *radio groups*. Only one radio button in a same-named group of radio buttons can be checked at a time. Selecting any radio button in that group automatically deselects any currently-selected radio button in the same group. -->

<!--  'value' 
is the value sent to the server if it is checked -->

<input type="radio" name="pizza" id="salami" value="salami">
<label for="salami">Salami</label><br/>

<input type="radio" name="pizza" id="4seasons" value="4seasons">
<label for="4seasons">Four Seasons</label><br/>
```

#### type="checkbox"

```html
<input type="checkbox">
```

* let you turn individual values on and off
* `checked="checked"` # specifies that an input element should be pre-selected when the page loads
* `value="<value>"` # the value sent to the server if the checkbox has been checked


### auxiliar

#### type="hidden"

```html
<input type="hidden">
A control that is not displayed but whose value is submitted to the server.  
Lets web developers include data that cannot be seen or modified by users when a form is submitted.  
Holds a DOMString that contains the hidden data you want to include when the form is submitted to the server. This specifically can't be edited or seen by the user via the user interface, although you could edit the value via browser developer tools.
```

#### type="reset"
```html
<input type="reset">
A button that resets the contents of the form to default values. Not recommended.
```

### submit

```html
<!-- common attributes -->
<input type="submit|image">
<input type="submit|image" formaction="<url>">
specifies the URL of the file that will process the input control when the form is submitted
<input type="submit|image" formenctype="application/x-www-form-urlencoded">
<input type="submit|image" formenctype="multipart/form-data">
<input type="submit|image" formenctype="text/plain">
specifies how the form-data should be encoded when submitting it to the server
<input type="submit|image" formmethod="get|post">
defines the HTTP method for sending data to the action URL
<input type="submit|image" formtarget="_blank|_self|_parent|_top|<framename>">
specifies where to display the response that is received after submitting the form
<input type="submit|image" formnovalidate>
Bypass form control validation for form submission

<!-- type="submit" -->
<input type="submit">
Defines a submit button which submits all form values to a form-handler; the form-handler is typically a server page with a script for processing the input data and is specified in the form's `action` attribute

<!-- type="image" -->
<input type="image">
A graphical submit button. Displays an image defined by the src attribute. The alt attribute displays if the image src is missing.
<input type="image" height="10px" width="10px" alt="some text" src="<url>">
'src' specifies the URL of the image to use as a submit button
```

### number, range, date & time

```html
<!-- common attributes -->
<input type="number" step="<number>|any"> 
specifies the interval between legal numbers in an input field
<input type="number" max="<number>|<date>">
specifies the maximum value for an input element
<input type="number" min="<number>|<date>">
specifies a minimum value for an input element

<input type="number">
A control for entering a number. Displays a spinner and adds default validation when supported.

<input type="range">
let the user specify a numeric value which must be no less than a given value, and no more than another given value. The precise value, however, is not considered important. This is typically represented using a slider or dial control rather than a text entry box like the number input type. Because this kind of widget is imprecise, it shouldn't typically be used unless the control's exact value isn't important.

<!-- example #1 -->
<div class="slidecontainer">
  <input type="range" min="1" max="100" value="50" class="slider" id="myRange">
</div>
<!-- example #2 -->
<input type="range" value="value" step="value" max="value" min="value" onchange="function()">

<input type="time">
<input type="month">
<input type="week">
<input type="date">
<input type="datetime-locale">
```

## `<link>`

It's not recommended to place `<link>` outside of `<head>`, because whatever content comes before `<link>` will start rendering and then when the stylesheet is loaded will be rerendered with the new styles. That means that the pageload will be slower (because the browser has to redo all that work) and uglier (because there will be this flash of content with one style that's then restyled to look different).

### hreflang

* each version of a page must link to all other versions, *including itself*
* alternate URLs must be fully-qualified, including the transport method (http/https), 
  i.e: `https://example.com/foo`

```html
<link rel="alternate" hreflang="en" href="https://example.com" />
<link rel="alternate" hreflang="es" href="https://es.example.com" />
<link rel="alternate" hreflang="de" href="https://de.example.com" />

<!-- For pages that allow users to select their language, use the x-default keyword: -->
<link rel="alternate" href="https://example.com" hreflang="x-default" />
```

### browser resource hints

- `preload` – load content that's required for the intial render
- `prefetch` - load content that may be needed to render the next page
- `preconnect` - establish a server connection without loading a specific resource yet

#### prefetch

`<link rel="prefetch">` is a directive that tells a browser to **fetch a resource that will probably be needed** for the next navigation. That mostly means that the resource will be fetched with **extremely low priority** (since everything the browser *knows* is needed in the current page is more important than a resource that we *guess* might be needed in the next one). That means that prefetch’s main use  case is speeding up the next navigation rather than the current one.

#### preload

* lets you declare fetch requests specifying resources that your page will need very soon, which you want to start loading early in the page lifecycle, before browsers' main rendering machinery kicks in.
* This ensures they are available earlier and are less likely to block the page's render, improving performance. 
* Even though the name contains the term *load*, it doesn't load and execute the script but only schedules it to be downloaded and cached with a higher priority.

```html
<link rel="preload" as="style" href="async_style.css" onload="this.rel='stylesheet'">
```

```html
<link rel="preload" as="style" href="styles.css" onload="this.onload=null;this.rel='stylesheet'">
<noscript><link rel="stylesheet" href="styles.css"></noscript>
```

- link **rel="preload" as="style"** requests the stylesheet asynchronously.
- The **onload** attribute in the link allows the CSS to be processed when it finishes loading.
- "nulling" the onload handler once it is used helps some browsers avoid re-calling the handler upon switching the rel attribute.
- The reference to the stylesheet inside of a **noscript** element works as a fallback for browsers that don't execute JavaScript.

```html
<link rel="preload" as="image" href="/assets/imgs/ex.webp">
```

### canonical

* By adding `rel=canonical`, you indicate what the *canonical URL* is, identifying the preferred version of a web page and facilitating its indexing.
* Placed it in the `<head>` section of both the canonical and duplicate pages

The most common example of duplicate content is related to the homepage:

- `example.com`
- `www.example.com`
- `example.com/index.html`
- `www.example.com/index.html`

All these URLs lead to the same resource, which is the site’s homepage. 

Suppose you want `https://example.com/dresses/green-dresses` to be the canonical URL, even though a variety of URLs can access this content. Indicate this URL as canonical with these steps:

```html
<html>
<head>
  <title>Explore the world of dresses</title>
  <link rel="canonical" href="https://example.com/dresses/green-dresses" />
</head>
<!-- rest of the HTML -->
```

Use absolute paths rather than relative paths with the `rel="canonical"` `link` element. Even though relative paths are supported by Google, they can cause problems in the long run

## `<mark>`

* highlight search results, key terms, or any content requiring immediate attention

```html
<p>
  In the field of technology, adaptability is <mark>crucial</mark> for staying ahead of the curve.
</p>
```

## `<menu>` 

a semantic alternative to `<ul>`, but treated by browsers (and exposed through the accessibility tree) as no different than `<ul>`. It represents an unordered list of items.

`<nav>` should contain links to help users navigate through web pages, whereas the `<menu>` element should help users carry out certain tasks.

```html
<menu>
  <li><button onclick="copy()">Copy</button></li>
  <li><button onclick="cut()">Cut</button></li>
  <li><button onclick="paste()">Paste</button></li>
</menu>
```

## `<ol>` | `<ul>` | `<li>`

nested lists:

```html
<ul>
 <li> Dogs 
   <ul>
     <li> Siberian Husky </li>
     <li> German Shepherd </li>
   </ul> 
 </li>
 <li> Cats </li>
 <li> Hamster </li> 
</ul> 
```

## `<output>`

a container element into which a site or app can inject the results of a calculation or the outcome of a user action.

```html
<output for="<id>" form="" name=""></output>

<form oninput="result.value=parseInt(a.value)+parseInt(b.value)">
  <input type="range" id="b" name="b" value="50" /> +
  <input type="number" id="a" name="a" value="10" /> =
  <output name="result" for="a b">60</output>
</form>

```

* `for="element_id"` # A space-separated list of ids. Specifies the relationship between the result of the calculation, and the elements used in the calculation. 
* `form="form_id"` # Specifies which form the output element belongs to
* `name="name"` # Specifies a name for the output element

## `<q>`

Enclose short inline quotations. It is particularly useful when you want to mark a specific portion of text as a quote within a paragraph or a  line of text.

```html
<p>
  The famous physicist Albert Einstein once said, <q>E=mc²</q>, emphasizing the relationship between energy (E), mass (m), and the speed of light (c).
</p>
```

`<script>`
-------------------------------

`<script>` # used to define a client-side script (JavaScript).

```html
<!-- internal / inlined -->
<script></script>
<!-- external / linked -->
<script src='path'></script>
```

There are several ways an external script can be executed:

* `async="async"` # the script is executed asynchronously with the rest of the page (the script will be executed while the page continues the parsing)
* if *async* is not present and `defer="defer"` # the script is executed when the page has finished parsing
* If neither *async* or *defer* is present: the script is fetched and executed immediately, before the browser continues parsing the page

The `type` attribute is required in HTML 4, but optional in HTML5.  
The `async` attribute is new in HTML5.  

### attributes

```html
<!-- The script is executed asynchronously (only for external scripts) -->
<script async="async"></script>
<!-- The character encoding used in an external script file -->
<script charset="charset"></script>
<!-- The script is executed when the page has finished parsing (only for external scripts) -->
<script defer="defer"></script>
<!-- The URL of an external script file -->
<script src="url"></script>
<!-- The media type of the script -->
<script type="media_type"></script>
```

### `type="importmap"`

* key: the name of the import specifier
* value: the relative or absolute URL to which the specifier should map:
  `/lib/my-module.js` or `../lib/my-module.js`, or `./lib/my-module.js`
* A module that is not utilized by a script on the page will not be  loaded by the browser, even if it is present in the import map.

#### import map in external file

```html
<script type="importmap" src="importmap.json"></script>
```

* Ensure the file is sent with its `Content-Type` header set to `application/importmap+json`
* The inline approach is recommended for performance reasons

#### map a specifier to an entire package

Aside from mapping a specifier to a module, you can also map one to a package that contains several modules. 
This is done by using specifier  keys and paths that **end with a trailing slash**.

```html
<script type="importmap">
{
  "imports": {
    "lodash/": "/node_modules/lodash-es/"
  }
}
</script>
```

This technique allows you to import any module in the specified path instead of the entire main module, which causes all component modules to be downloaded by the browser.

```html
<script type="module">
  import toUpper from 'lodash/toUpper.js';
  import toLower from 'lodash/toLower.js';

  console.log(toUpper('hello'));
  console.log(toLower('HELLO'));
</script>
```

#### map syntax

The module specifier map JSON object has the following requirements:

- None of the keys may be empty.
- All of the values must be strings, defining either a valid absolute URL or a valid URL string that starts with `/`, `./`, or `../`.
- If a key ends with `/`, then the corresponding value must also end with `/`. A key with a trailing `/` can be used as a prefix for when mapping (or remapping) modules addresses.          
- The object properties' ordering is irrelevant: if multiple keys can match the module specifier, the most specific key is used (in  other words, a specifier "olive/branch/" would match before "olive/").

### loading scripts

When the browser loads HTML and comes across a `<script>...</script>` tag, it can’t continue building the DOM. It must execute the script right now. The same happens for external scripts: the browser must wait for the script to download, execute the downloaded script, and only then can it process the rest of the page.

That leads to two important issues:

* Scripts can’t see DOM elements below them, so they can’t add handlers etc.
* If there’s a bulky script at the top of the page, it “blocks the page”. Users can’t see the page content till it downloads and runs

Workaround (before there was `defer`): 

Put a script at the bottom of the page. Then it can see elements above it, and it doesn’t block the page content from showing.  

Drawback: The browser notices the script (and can start downloading it) only after it downloaded the full HTML document. For long HTML documents, that may be a noticeable delay.

#### defer

- Scripts with `defer` never block the page.
- Scripts with `defer` always execute when the DOM is ready (but before `DOMContentLoaded` event)

`defer` tells the browser *not to wait* for the script. Instead, the browser will continue to process the HTML, build DOM. The script loads “in the background”, and then runs when the DOM is fully built.

* Scripts with `defer` never block the page.
* Scripts with `defer` always execute when the DOM is ready but before the *DOMContentLoaded* event
* only for *external scripts*
* Deferred scripts keep their relative order - just like regular scripts.
  Usage:
* scripts that need the whole DOM and/or their relative execution order is important.

```html
<p>...content before scripts...</p>
<script>
  document.addEventListener('DOMContentLoaded', () => alert("DOM ready after defer!"));
</script>
<script defer src="https://javascript.info/article/script-async-defer/long.js?speed=1"></script>
<p>...content after scripts...</p>
```

* The page content shows up immediately.
* *DOMContentLoaded* event handler waits for the deferred script. It only triggers when the script is downloaded and executed.

#### async

The `async` attribute means that a script is completely independent:

- The browser doesn’t block on `async` scripts (like `defer`).

- They don’t wait for anything, nothing waits for them.

- The script that loads first – runs first (“load-first” order)

- Usage: independent scripts, like *counters*, *ads*, document-level event listeners 

- `DOMContentLoaded` and async scripts don’t wait for each other:

  `DOMContentLoaded` may happen both *before* an async script (if an async script finishes loading after the page is complete) or *after* an async script (if an async script is short or was in HTTP-cache)

In other words, `async` scripts load in the background and run when ready. The DOM and other scripts don’t wait for them, and they don’t wait for anything. A fully independent script that runs when loaded.

For **module scripts**, if `async` is present, then the module script and all its dependencies will be fetched in parallel to parsing, and the module script will be evaluated as soon as it is available (potentially before parsing completes). Otherwise, the module script and its dependencies will be fetched in parallel to parsing and evaluated when the page has finished parsing. (The defer attribute has no effect on module scripts.)

#### page without scripts should be usable

Please note: if you’re using `defer` or `async`, then user will see the the page before the script loads.  
In such case, some graphical components are probably not initialized yet.  
Don’t forget to put “*loading*” indication and disable buttons that aren’t functional yet. Let the user clearly see what he can do on the page, and what’s still getting ready.

#### type="module"

* implies `defer`
* unlike classic scripts, module scripts require the use of the CORS protocol for cross-origin fetching

## `<section>`

* sections grouped around a specific theme
* a thematic grouping of content, typically with a heading
* each `<section>` ***should be identified, typically by including a heading*** as a child, wherever possible
* defines a `region` landmark when it has an accessible name (`aria-labelledby`, `aria-label` or `title`)
* are like book chapters
  - they usually have siblings (maybe in a different document?)
  - together they have something in common

`<section>`s in an `<article>` are like chapters in a book

`<article>`s in a `<section>` are like poems in a volume (within a series)

```html
<section>
  <h2>Heading</h2>
  <p>Bunch of awesome content</p>
</section>
```

### headings and sections

Authors are also encouraged to explicitly wrap sections in elements of sectioning content, instead of relying on the implicit sections generated by having multiple headings in one element of sectioning content.

```html
<!-- For example, the following is correct: -->
<body>
 <h4>Apples</h4>
 <p>Apples are fruit.</p>
 <section>
  <h2>Taste</h2>
  <p>They taste lovely.</p>
  <h6>Sweet</h6>
  <p>Red apples are sweeter than green ones.</p>
  <h1>Color</h1>
  <p>Apples come in various colors.</p>
 </section>
</body>
```

However, the same document would be more clearly expressed as:

```html
<body>
  <h1>Apples</h1>
  <p>Apples are fruit.</p>
  <section>
    <h2>Taste</h2>
    <p>They taste lovely.</p>
    <section>
      <h3>Sweet</h3>
      <p>Red apples are sweeter than green ones.</p>
    </section>
  </section>
  <section>
    <h2>Color</h2>
    <p>Apples come in various colors.</p>
  </section>
</body>
```

This third example is also semantically identical, and might be easier to maintain (e.g. if sections are often moved around in editing):

This final example would need explicit style rules to be rendered well in legacy browsers. Legacy browsers without CSS support would render all the headings as top-level headings.

```html
<body>
  <h1>Apples</h1>
  <p>Apples are fruit.</p>
  <section>
    <h1>Taste</h1>
    <p>They taste lovely.</p>
    <section>
      <h1>Sweet</h1>
      <p>Red apples are sweeter than green ones.</p>
    </section>
  </section>
  <section>
    <h1>Color</h1>
    <p>Apples come in various colors.</p>
  </section>
</body>
```

```html
<article>
  <section></section>	
  <section></section>	
  <section></section>	
</article>
<!-- nesting sections inside an article of content is logical for a machine, and also for a human (code will be easier to read for the developer).-->
```

`<select>` |`<optgroup>`
--------------------------

used to create a drop-down list; a form control and is most used in a form to collect user input.

```html
<select
	multiple
	required
	form="<form-id>"
	size="<number>"
	>
	<option
		value="value">
		text
	</option>
</select>
```

```html
<select>
   <optgroup label="Fruits">
      <option>Apple</option>
      <option>Banana</option>
      <option>Mango</option>
   </optgroup>
   <optgroup label="Vegetables">
      <option>Tomato</option>
      <option>Broccoli</option>
      <option>Carrot</option>
   </optgroup>
</select>
```

### attributes

`multiple` # specifies that multiple options can be selected at once
`required` # specifies that the user is required to select a value before submitting the form
`size=<number>` # defines the number of visible options in a drop-down list
`form=<form-id>` # defines which form the drop-down list belongs to

### `<option>`

define the available options in the drop-down list.

* `value="value"` # contains the data value to submit to the server when that option is selected. If no value attribute is included, the value defaults to the text contained inside the element

### example

```html
<select name="cars" id="cars">
  <option value="volvo">Volvo</option>
  <option value="saab">Saab</option>
  <option value="mercedes">Mercedes</option>
  <option value="audi">Audi</option>
</select>
```

## `<small>`

The copyright notice should be contained in a small element:

> […] typically features disclaimers, caveats, legal restrictions, or copyrights.

If the copyright notice applies to the content of a sectioning content element (e.g., `article`), include the `small` element in a `footer` element that belongs to this sectioning element:

`<table>`
-----------------------------------------------

* A table is defined using the `<table>` element
* It contains a number of `<td>` table cells (“table data”) 
* They are organized into `<tr>` table rows
* The markup for a table is *always based on rows, never columns*.
* `<th>` table header; Table cells which act as column headers or row headers
* Table cells can be merged using the `colspan` and `rowspan` attributes.
* Tables can be broken into *sections* using the following elements:
  * `<thead>` — Table header
  * `<tbody>` — Table body
  * `<tfoot>` — Table footer
* A caption can be added to a table using the `<caption>` element.
* You can use `<col>` and `<colgroup>` to define table columns for styling. However, there are a number of limitations with this practice.

Read more: https://html.com/tables/#ixzz6pAzTQuYG

```html
<table>
	<tr>
		<th>Firstname</th>
		<th>Lastname</th>
		<th>Age</th>
	</tr>
	<tr>
		<td>Jill</td>
		<td>Smith</td>
		<td>50</td>
	</tr>
	<tr>
		<td>Eve</td>
		<td>Jackson</td>
		<td>94</td>
	</tr>
</table>
```

MEDIA
===========


\<img\>
-----------

has two required attributes: `src` and `alt`
* Images are not technically inserted into an HTML page, images are linked to HTML pages. The `<img>` tag creates a holding space for the referenced image.
* To link an image to another document, simply nest the `<img>` tag inside `<a>` tags
* In HTML the `<img>` tag has no end tag.

```html
<a href="url">
	<img border="0" alt="name" src="path" width="100" height="100">
</a>
```

### attributes

```html
<img alt="<text>"> <!-- an alternate text for an image -->
<img width="<pixels>"> <!-- the width of an image -->
<img height="<pixels>"> <!-- an alternate text for an image -->
<img src="<url>"> <!-- the URL of an image -->
<img srcset="<url>"> <!-- the URL of the image to use in different situations -->
<img referrerpolicy="no-referrer|no-referrer-when-downgrade|origin|origin-when-cross-origin|unsafe-url"> <!-- specifies which referrer to use when fetching the image -->

<img crossorigin="anonymous|use-credentials"> <!-- allow images from third-party sites that allow cross-origin access to be used with canvas -->
<img ismap="<ismap>"> <!-- image as a server-side image-map -->
<img usemap="<#mapname>"> <!-- image as a client-side image-map -->
<img longdesc="<url>"> <!-- an alternate text for an image -->
<img sizes="<sizes>"> <!-- image sizes for different page layouts -->
```

### author attribution

`figure` is a sectioning root element, which means that `footer` (and `header`) can apply to it:

> The `footer` element represents a footer **for its nearest** ancestor sectioning content or **sectioning root element**.

So you could include a `footer` element (containing a `small` element) in the `figure`:

```html
<figure>
  <img src="img/content/preview.jpg" alt="Alttext für das Bild" />
  <footer><small><!-- copyright noice --></small></footer>
  <figcaption>Caption goes here</figcaption>
</figure>
```

Structurally, this copyright notice would apply to the whole content of the `figure` element. (It’s also possible to include the `footer` in the `figcaption`.)

### resolution switching: `srcset` & `sizes` [different sizes]

Define the set of images we will allow the browser to choose between, and what size each image is. 

```html
<!-- srcset: the available images that may be called depending on the following device width -->
<!-- sizes:  the media breakpoints (device width) -->
<img
	 srcset="elva-fairy-480w.jpg 480w, elva-fairy-800w.jpg 800w"
	 sizes="(max-width: 600px) 480px, 800px" 
	 src="elva-fairy-800w.jpg" 
	 alt="Elva dressed as a fairy"
>
```
#### srcset

An image filename (`elva-fairy-480w.jpg`) followed by the image's intrinsic width in pixels (`480w`) — note that this uses the w unit, not px as you might expect. This is the image's real size, which can be found by inspecting the image file on your computer.

#### sizes

A media condition `(max-width:600px)` followed by the width of the slot the image will fill when the media condition is true (`480px`)

* You may provide an absolute length (px, em) or a length relative to the viewport (vw), but not percentages.
* The last slot width has no media condition (this is the default that is chosen when none of the media conditions are true).
* The browser ignores everything after the first matching condition, so be careful how you order the media conditions.

```html
<img srcset="elva-fairy-480w.jpg 480w, elva-fairy-800w.jpg 800w" sizes="96vw">
 <!-- “This image is going to be pretty big on the page — almost the full width — but there will always be a little padding around the edges, so not quite. --> 
<img srcset="elva-fairy-480w.jpg 480w, elva-fairy-800w.jpg 800w" sizes="(min-width: 1000px) 33vw, 96vw">
<!-- “This image is in a three-column layout on large screens and close to full-width otherwise.” -->
```

#### procedure

The browser will...

1. Look at its device width.
2. Work out which media condition in the sizes list is the first one to be true.
3. Look at the slot size given to that media query.
4. Load the image referenced in the srcset list that has the same size as the slot or, if there isn't one, the first image that is bigger than the chosen slot size.

#### pragmatic solution 1

* *1920px* (this covers FullHD screens and up)
* *1600px* (this will cover 1600px desktops and several tablets in portrait mode, for example iPads at 768px width, which will request a 2x image of 1536px and above)
* *1366px* (it is the most widespread desktop resolution)
* *1024px* (1024x768 screens are rarer, but I think you need some image size in between, not to leave too big a gap between pixel sizes, in case the market changes)
* *768px* (useful for 2x 375px mobile screens, as well as any device that actually requests something close to 768px)
* *640px* (for smartphones)

This leaves you covered for most cases. If you think that 640px is too high a minimum, keep in mind that most smartphones on the market now have high pixel density, and even though they declare 320px of viewport width, they will support double that at minimum, therefore 640px.
The above sizes are meant as a reference for 100vw images. If an image covers less screen width in your design, you need to divide the above numbers by the actual (approximate) width of the image on screen.

Most used smartphone screen resolutions in Germany 2019 

1.	1080x1920	17.89%
2.	750x1334	17.09%
3.	1440x2960	12.40%
4.	720x1280	11.56%
5.	1440x2560	9.71%

### resolution switching: `srcset` [different resolutions]

Allow the browser to choose an appropriate resolution image by using `srcset` with `x`-descriptors and without `sizes`

```html
<img 
	srcset="elva-fairy-320w.jpg, elva-fairy-480w.jpg 1.5x, elva-fairy-640w.jpg 2x"
  src="elva-fairy-640w.jpg"
  alt="Elva dressed as a fairy"
>
```

If the device accessing the page has a standard/low resolution display, with one device pixel representing each CSS pixel, the elva-fairy-320w.jpg image will be loaded (the 1x is implied, so you don't need to include it.) If the device has a high resolution of two device pixels per CSS pixel or more, the elva-fairy-640w.jpg image will be loaded.


### Art direction: `<picture>`

Like `<video>` and `<audio>`, the `<picture>` element is a wrapper containing several `<source>` elements that provide different sources for the browser to choose from, followed by the all-important `<img>` element.

```html
<picture>
	<source media="(max-width: 799px)" srcset="elva-480w-close-portrait.jpg">
	<source media="(min-width: 800px)" srcset="elva-800w.jpg">
	<img src="elva-800w.jpg" alt="Chris standing up holding his daughter Elva">
</picture>
```

* The `<source>` elements include a media attribute that contains a media condition — as with the first srcset example, these conditions are tests that decide which image is shown — the first one that returns true will be displayed. 
* The srcset attributes contain the path to the image to display. Just as we saw with `<img>` above, `<source>` can take a srcset attribute with multiple images referenced, as well as a sizes attribute. So, you could offer multiple images via a `<picture>` element, but then also offer multiple resolutions of each one.
* In all cases, you must provide an `<img>` element, with src and alt, right before `</picture>`, otherwise no images will appear. This provides a default case that will apply when none of the media conditions return true

> Note: You should use the `media` attribute only in art direction scenarios; when you do use media, don't also offer media conditions within the sizes attribute.

#### use cases

* For most responsive images, you shouldn’t use `<picture>`. You should use `srcset` and/or `sizes`.
* When you specify the media queries within `<picture>`, you are providing rules to the browser that it *must* follow. The browser has no discretion to make smart decisions about what to download based on user preference, network, etc.
* only use it when you’re solving for *art direction*, not for *resolution switching*.



\<map\>
-----------

### Preliminary

When should I NOT image maps?
* When your image is responsive and changes size
When should I use image maps?
* When your images won’t change size
* When your link needs to be a complex shape, and not just a box
* When you’re dealing with raster images like jpg photographs


* `<map>` # used to define an **image map**. An image map is an image with clickable areas.
* The required `name` attribute of the `<map>` element is associated with the `<img>`'s `usemap` attribute and creates a relationship between the image and the map.
* The `<map>` element contains a number of `<area>` elements, that defines the clickable areas in the image map.

```html
<img src="workplace.jpg" alt="Workplace" usemap="#workmap" width="400" height="379">

<map name="workmap">
  <area shape="rect" coords="34,44,270,350" alt="Computer" href="computer.htm">
  <area shape="rect" coords="290,172,333,250" alt="Phone" href="phone.htm">
  <area shape="circle" coords="337,300,44" alt="Cup of coffee" href="coffee.htm">
</map>
```


GLOBAL ATTRIBUTES
===============

can be used on any HTML element.

* `accesskey`       # Specifies a shortcut key to activate/focus an element
* `class`           # Specifies one or more classnames for an element (refers to a class in a style sheet)
* `contenteditable` # Specifies whether the content of an element is editable or not
* `data-*`          # Used to store custom data private to the page or application
* `dir`             # Specifies the text direction for the content in an element
* `draggable`       # Specifies whether an element is draggable or not
* `dropzone`        # Specifies whether the dragged data is copied, moved, or linked, when dropped
* `hidden`          # Specifies that an element is not yet, or is no longer, relevant
* `id`              # Specifies a unique id for an element
* `lang`            # Specifies the language of the element's content
  * `<p lang="fr">Ceci est un paragraphe.</p>` #
* `spellcheck`      # Specifies whether the element is to have its spelling and grammar checked or not
* `style`           # Specifies an inline CSS style for an element
  * `<tag style="property:value;">` # The property is a CSS property. The value is a CSS value.
* `tabindex`        # Specifies the tabbing order of an element
* `title`           # Specifies extra information about an element
* `translate`       # Specifies whether the content of an element should be translated or not

## inert

* can also be added to elements that  should be offscreen or hidden. 
* An inert element, along with its  descendants, gets removed from the tab order and accessibility tree.
* can be added to sections of content that should not be interactive. When an element is inert, it along with all of the element's descendants,  including normally interactive elements such as links, buttons, and form controls are disabled because they cannot receive focus or be clicked.

### tabindex

indicates that its element can be focused, and where it participates in sequential keyboard navigation (usually with the Tab key, hence the name).

```html
<div tabindex="0"></div>
The element should be focusable in sequential keyboard navigation, after any positive tabindex values and its order is defined by the document's source order.

<div tabindex="-1"></div>
A negative value means that the element is *not reachable* via sequential keyboard navigation, but could be focused with Javascript or visually by clicking with the mouse. It's mostly useful to create accessible widgets with JavaScript.
```

* `tabindex="<n>"`  # Any positive value means the element should be focusable in sequential keyboard navigation, with its order defined by the value of the number.  
  * That is, tabindex="4" is focused before tabindex="5" and tabindex="0"
  * If multiple elements share the same positive tabindex value, their order relative to each other follows their position in the document source.
  * Avoid using positives tabindices. Doing so makes it difficult for people who rely on assistive technology to navigate and operate page content.
