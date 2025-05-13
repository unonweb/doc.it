PWA
=======================

* must be served via *https*

service worker
--------------------

```js
// main.js
// ---------------------------------------

window.onload = () => {
  	'use strict';
	if ('serviceWorker' in navigator) {
		navigator.serviceWorker.register('./sw.js');
	}
}
// sw.js
// -------------------------------------

var cacheName = 'hello-pwa';
var filesToCache = [
	'/',
	'/index.html',
	'/css/style.css',
	'/js/main.js'
];

/* Start the service worker and cache all of the app's content */
self.addEventListener('install', function(e) {
	e.waitUntil(
		caches.open(cacheName).then(function(cache) {
		return cache.addAll(filesToCache);
		})
	);
});

/* Serve cached content when offline */
self.addEventListener('fetch', function(e) {
	e.respondWith(
		caches.match(e.request).then(function(response) {
		return response || fetch(e.request);
		})
	);
});
```

manifest.json
-------------------------------------------

```json
"name": "" // The title of the app. This is used when prompting the user to install the app. It should be the full title of the app.
"short_name": "" // The name of the app as it will appear on the app icon. This should be short and to the point.
"lang": "" // The default language the app is localized in. In our case, English.
"start_url": "index.html",
// Tells the browser which page to load up when the app is launched. This will usually be index.html but it doesn’t need to be.

"icons": [
    {
        "src": "img/android-chrome-192x192.png",
        "sizes": "192x192",
        "type": "image/png"
    },
    {
        "src": "img/android-chrome-512x512.png",
        "sizes": "512x512",
        "type": "image/png"
    }
],

/* For Chromium, you must provide at least a **192x192** pixel icon, and a  **512x512** pixel icon. 
If only those two icon sizes are provided, Chrome will automatically scale the icons to fit the device. If you'd prefer to scale your own icons, and adjust them for pixel-perfection, provide  icons in increments of 48dp. */


"display": "" // The type of shell the app should appear in. 
"display": "standalone" // The app runs in its own window, separate from the browser, and hides standard browser UI elements like the URL bar.
"display": "fullscreen"	// Opens the web application without any browser UI and takes up the entirety of the available display area.
"display": "minimal-ui" // This mode is similar to standalone, but provides the user a minimal set of UI elements for controlling navigation (such as back and reload).
"display": "browser" // A standard browser experience.

"background_color": "", 
// The color of the splash screen that opens when the app launches.
"theme_color": "",
/* The theme_color sets the color of the tool bar, and may be reflected in the app's preview in task switchers. The theme_color should match the meta theme color specified in your document head. */

"scope": ""

/* The scope defines the set of URLs that the browser considers to be within your app, and is used to decide when the user has left the app. The scope controls the URL structure that encompasses all the entry and exit points in your web app. Your start_url must reside within the scope. */
```

#### scope

- If you don't include a `scope` in your manifest, then the default implied `scope` is the directory that your web app manifest is served from.
- The `scope` attribute can be a relative path (`../`), or any higher level path (`/`) which would allow for an increase in coverage of navigations in your web app.
- The `start_url` must be in the scope.
- The `start_url` is relative to the path defined in the `scope` attribute.
- A `start_url` starting with `/` will always be the root of the origin.

### key properties

```json
{
 	"short_name": "",
	"name": "",
    "start_url": "index.html",
    "icons": [
        {
        "src": "img/android-chrome-192x192.png",
        "sizes": "192x192",
        "type": "image/png"
    },
    {
        "src": "img/android-chrome-512x512.png",
        "sizes": "512x512",
        "type": "image/png"
    }
    ],
    "background_color": "#3367D6",
    "theme_color": "#3367D6",
    
    "display": "browser",
}
```

#### icons

For Chromium, you must provide at least a **192x192** pixel icon, and a  **512x512** pixel icon. 
If only those two icon sizes are provided, Chrome will automatically scale the icons to fit the device. If you'd prefer to scale your own icons, and adjust them for pixel-perfection, provide  icons in increments of 48dp.

### example 1

```json
{
  "short_name": "Weather",
  "name": "Weather: Do I need an umbrella?",
  "icons": [
    {
      "src": "/images/icons-vector.svg",
      "type": "image/svg+xml",
      "sizes": "512x512"
    },
    {
      "src": "/images/icons-192.png",
      "type": "image/png",
      "sizes": "192x192"
    },
    {
      "src": "/images/icons-512.png",
      "type": "image/png",
      "sizes": "512x512"
    }
  ],
  "start_url": "/?source=pwa",
  "background_color": "#3367D6",
  "display": "standalone",
  "scope": "/",
  "theme_color": "#3367D6",
  "shortcuts": [
    {
      "name": "How's weather today?",
      "short_name": "Today",
      "description": "View weather information for today",
      "url": "/today?source=pwa",
      "icons": [{ "src": "/images/today.png", "sizes": "192x192" }]
    },
    {
      "name": "How's weather tomorrow?",
      "short_name": "Tomorrow",
      "description": "View weather information for tomorrow",
      "url": "/tomorrow?source=pwa",
      "icons": [{ "src": "/images/tomorrow.png", "sizes": "192x192" }]
    }
  ],
  "description": "Weather forecast information",
  "screenshots": [
    {
      "src": "/images/screenshot1.png",
      "type": "image/png",
      "sizes": "540x720"
    },
    {
      "src": "/images/screenshot2.jpg",
      "type": "image/jpg",
      "sizes": "540x720"
    }
  ]
}
```



images
---------------------

### app icon

a minimum of 7 different sizes: 
* 128x128 px
* 144x144 px
* 152x152 px
* 192x192 px
* 256x256 px
* 512x512px 

### favicon
* 16x16px

Place the *favicon.ico* in the project root folder.

html
---------------

### head

```html
<!-- include the manifest.json -->
<link rel="manifest" href="/manifest.json">
<!-- include the favicon -->
<link rel="icon" href="favicon.ico" type="image/x-icon" />
<!-- include the manifest.json -->
<link rel="apple-touch-icon" href="images/hello-icon-152.png">   
<meta name="theme-color" content="white"/>  
<meta name="apple-mobile-web-app-capable" content="yes">  
<meta name="apple-mobile-web-app-status-bar-style" content="black"> 
<meta name="apple-mobile-web-app-title" content="Hello World"> 
<meta name="msapplication-TileImage" content="images/hello-icon-144.png">  
<meta name="msapplication-TileColor" content="#FFFFFF">
```