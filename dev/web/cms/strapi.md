# API

## REST

```js
fetch('http://localhost:1337/api/articles/1')
    .then(response => response.text())
    .then(data => console.log(data))

// all in one
fetch('http://localhost:1337/api/articles', {
    method: 'get', 
    headers: { 
		'Authorization': 'frida a3f9aea5c231409f14488a82e92dc08239fdf98cfab1eaf5ba070a5191c59cf5feb920d4de28cb527b70a84e0fb10e9a7dc3e40666d1e106bb7878a84a6335a80b4cd788c8fe6bda06fa407a1934ebb0469dc3d24a3495cf78aaf67b0ce6a8f42807e8f98868cc6bcf62d657b56405578d154d772b2e2d250e0d1d726daba517'
	}
})
```

## Localization

```js
?populate=localizations
http://localhost:1337/api/articles?populate=localizations
```



## Query engine

```js
findOne() // Finds the first entry matching the parameters
const entry = await strapi.db.query('api::blog.article').findOne({
  select: ['title', 'description'],
  where: { title: 'Hello World' },
  populate: { category: true },
})
```

# CONFIG

## middleware

```js
// file: root_project/config/middleware.js:

module.exports = [
  //...
  {
    name: 'strapi::cors',
    config: {
      enabled: true,
      header: '*',
      origin: ['http://localhost:8080']
    }
  }
  //...
];
```

```js
// path: ./config/middlewares.js

module.exports = [
  // The array is pre-populated with internal, built-in middlewares, prefixed by `strapi::`
  'strapi::cors',
  'strapi::body',
  'strapi::errors',
  // ...
  'my-custom-node-module', // custom middleware that does not require any configuration
  {
    // custom name to find a package or a path
    name: 'my-custom-node-module',
    config: {
      foo: 'bar',
    },
  },
  {
    // custom resolve to find a package or a path
    resolve: '../some-dir/custom-middleware',
    config: {
      foo: 'bar',
    },
  },
];
```

# CLI

```sh
npx strapi config:dump | jq

curl localhost:1337 --head

curl -H 'Origin: http://localhost:1337' -H 'Authorization: frida a3f9aea5c231409f14488a82e92dc08239fdf98cfab1eaf5ba070a5191c59cf5feb920d4de28cb527b70a84e0fb10e9a7dc3e40666d1e106bb7878a84a6335a80b4cd788c8fe6bda06fa407a1934ebb0469dc3d24a3495cf78aaf67b0ce6a8f42807e8f98868cc6bcf62d657b56405578d154d772b2e2d250e0d1d726daba517' --verbose http://localhost:1337/api/articles

curl http://localhost:1337/api/articles

```

