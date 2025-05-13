# FONTS

## fonts-size

For  responsive websites, it's best to use a font size between **16px** and **20px**.

- **Body fonts should be around 16px.** When choosing your default font size (i.e., the one the majority of your  paragraphs, lists, and other content will use), 16px is a good starting point. Consider a smaller size for an interaction-heavy page or a font that is naturally larger. Use a bigger font if your client's page is  text-heavy.
- **Text inputs are at least 16px**. If you're designing a mobile web  app, iOS browsers will zoom in on the left side of text inputs that are smaller than 16px.

## font-family

### web safe fonts

* A web safe font is one that comes installed by default on virtually every computer or mobile device.
* You actually don’t need to add web safe fonts to a website!  

### font stack

Since you can't guarantee the availability of the fonts you want to use on your webpages, you can supply a font stack so that the browser has multiple fonts it can choose from. If the browser does not support the first font, it tries the next font, and so on. 

Start with the font you want, and end with a generic family

`font-family: prefered fallback1 fallback2 generic`  

```css
html {
    font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode';
    font-family: Geneva, Verdana;
    font-family: sans-serif;
    
    /* font stack */
    /* start with the font you want, and end with a generic family */
    font-family: prefered fallback1 fallback2 generic;
    font-family: "Trebuchet MS", Verdana, sans-serif;
    
    /* generic */
    font-family: serif, sans-serif, monospace, cursive, fantasy;
    
    /* serif */
    font-family: Times New Roman, Courier, Georgia, American Typewriter;    
    
    /* fantasy */
    font-family: Luminari;
    
    /* cursive */
    font-family: Bradley Hand, Brush Script MT, Comic Sans MS;
}
```

## formats

**WOFF2, and WOFF as a fallback, is the only font format you need in 2021.**

## font-weight

- 100 - Thin
- 200 - Extra Light (Ultra Light)
- 300 - Light
- 400 - Normal
- 500 - Medium
- 600 - Semi Bold
- 700 - Bold
- 800 - Extra Bold (Ultra Bold)
- 900 - Black (Heavy)

In order to see any effect using values other than `400` or `700`, the font being used must have built-in faces that match those specified weights.

The keyword values `bolder` and `lighter` are  relative to the computed font weight of the parent element. The browser  will look for the closest “bolder” or “lighter” weight, depending on  what is available in the font family, otherwise it will simply choose  “400” or “700”, depending on which makes the most sense.

## load

### local vs CDN

Google Fonts resources will be redownloaded for every website, regardless it being cached on the CDN. Self-host your fonts for better performance. The old performance argument is not valid anymore.

(Since Chrome v86, released October 2020, cross-site resources like fonts can’t be shared on the same CDN anymore. This is due to the *partitioned browser cache*)

### CDN

- Use `font-display: swap` if the host provides it.
- Otherwise, use FOUT with class

### local

1. `@font-face` + `font-display: swap`
2. FOUT with Class
3. Standard FOFT
4. Critical FOFT

**Choose `@font-face` + `font-display: swap`** if you’re starting out and don’t want to mess with JavaScript. It’s the simplest of them all. Also choose this option if you use only few font  files (fewer than two files) for each typeface.

**Choose Standard FOFT** if you’re ready to use JavaScript, but don’t want to do the extra work of subsetting the Roman font.

**Choose a Critical FOFT variant** if you want to go all the way for performance.

### best practices

* make sure that fonts get loaded as early as possible

### load in html

#### rel="stylesheet"

```html
<link rel="stylesheet" href="https://use.typekit.net/your-kit-id.css">
```

* Not the best approach. 
* The `href` blocks the browser. If loading the web font hangs, users won’t be able to do anything while they wait.

```html
<link
  href="https://fonts.googleapis.com/css2?family=Lato:ital,wght@0,400;0,700;1,400;1,700&display=swap"
  rel="stylesheet"
/>
```

* *slightly* better because it provides `font-display: swap`

Fonts defined with @font-face rules or background images defined in CSS files aren't discovered until the browser downloads and parses those CSS files. Preloading these resources ensures they are fetched before the CSS files have downloaded.

```html
<link rel="preload" href="/assets/myfont.woff" as="font">
<link rel="preload" href="ComicSans.woff2" as="font" type="font/woff2" crossorigin>

<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Open+Sans:ital,wght@0,300;0,400;0,700;1,300;1,400;1,700&display=swap" rel="stylesheet">
```

The first two `<link>` elements perform a task called a *preconnect*, which tells the browser to prioritize an external web connection. In turn, these two `<link>` elements prepare the browser to load the CSS file and font files from the third `<link>` as soon as possible.

### load in css

`@font-face` # specifies a custom font with which to display text; the font can be loaded from either a remote server or a locally-installed font on the user's own computer.

The @font-face rule should be added to the stylesheet before any styles

The available types are: `"woff"`, `"woff2"`, `"truetype"`, `"opentype"`, `"embedded-opentype"`, and `"svg"`.

### FOUT vs. FOUT with Class

https://css-tricks.com/the-best-font-loading-strategies-and-how-to-execute-them/

FOUT means **Flash Of Unstyled Text**. 
FOIT  means **Flash Of Invisible Text**

You always want FOUT over FOIT because it’s better for users to read words written with system fonts compared to words written with invisible ink. 

We mentioned earlier that font-display: swap gives you the ability to use FOUT natively.

## @font-face

**If we want to self-host fonts, we need to use `@font-face` to declare your fonts.**

**The `@font-face` at-rule may be used not only at the top level of a CSS!**

In this declaration, we can specify four things:

- `font-family`: This tells CSS (and JavaScript) the name of our font.
- `src`: Path to find the font so they can get loaded
- `font-weight`: The font weight. Defaults to 400 if omitted.
- `font-style`: Whether to italicize the font. Defaults to normal if omitted.

* **downloads fonts only if used**: When browsers parse an element with the web font, they trigger a download for the web font.

### how to

```css
/* create a @font-face rule */
@font-face {
	font-family: 'MyWebFont';
	src:  url('myfont.woff2') format('woff2'), url('myfont.woff') format('woff');
  font-weight: 400; /* default */
  font-style: normal; /* default */
}

/* use it */
body {
  	font-family: 'MyWebFont', Fallback, sans-serif;
}
```

#### formats

```css
@font-face {
  /* src & formats */
  font-family: 'myfont';
  src: url('myfont.otf') format('opentype'),
    url('myfont.ttf') format('truetype'),
    url('myfont.woff') format('woff'),
    url('myfont.woff2') format('woff2');
}
```

#### font-display

`font-display` takes one of four possible values: `auto`, `swap`, `fallback`, and `optional`. 

* `swap` 

  Gives the font face an extremely small block period and an infinite swap period. Instructs browsers to display the fallback text before web fonts get loaded. In other words, it triggers FOUT (Flash Of Unstyled Text) for self-hosted fonts. 

* `auto`

  The font display strategy is defined by the user agent.  

* `block`

  Gives the font face a short block period and an infinite swap period.  

* `fallback`

  Gives the font face an extremely small block period and a short swap period.  

* `optional`

  Gives the font face an extremely small block period and no swap period.  

#### properties

```css
@font-face {
	font-family: 'name'; 	/* Required. Defines a name for the font */
	
	/* happy medium between full support and practical support */
	src: url('webfont.eot'); /* IE9 Compat Modes */
	src: url('myfont.woff2') format('woff2'),
       	 url('myfont.woff') format('woff'),
       	 url('myfont.ttf') format('truetype');
	
	/* Defines how the font should be stretched */
	font-stretch: normal;
	font-stretch: condensed;
	font-stretch: ultra-condensed;
	font-stretch: extra-condensed;
	font-stretch: semi-condensed;
	font-stretch: expanded;
	font-stretch: semi-expanded;
	font-stretch: extra-expanded;
	font-stretch: ultra-expanded;

	/* Defines how the font should be styled. Default is "normal" */
	font-style:	normal;
	font-style:	italic;
	font-style:	oblique;

	/* Defines the boldness of the font. Default is "normal" */
	font-weight: normal;
	font-weight: bold;
	font-weight: 100;
	font-weight: 200;
	font-weight: 300;
	font-weight: 400;
	font-weight: 500;
	font-weight: 600;
	font-weight: 700;
	font-weight: 800;
	font-weight: 900;
	
	/* Defines the range of UNICODE characters the font supports. */
	unicode-range:	"U+0-10FFFF";
}
```

#### variations of a font-family

```css
/* Regular Weight */
@font-face {
    font-family: Montserrat;
    src: url("../fonts/Montserrat-Regular.ttf");
}
/* SemiBold (600) Weight */
@font-face {
    font-family: Montserrat;
    src: url("../fonts/Montserrat-SemiBold.ttf");
    font-weight: 600;
}
/* Bold Weight */
@font-face {
    font-family: Montserrat;
    src: url("../fonts/Montserrat-Bold.ttf");
    font-weight: bold;
}

/* Declared rules can be used by following */

body {
  /* Regular */
  font-family: Montserrat;
  /* Semi Bold */
  font-family: Montserrat;
  font-weight: 600;
  /* Bold */
  font-family: Montserrat;
  font-weight: bold;
}
```

