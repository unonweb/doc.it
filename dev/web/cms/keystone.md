### ui

```js
ui: {
    displayMode: 'cards',
    cardFields: ['name', 'email'],
    inlineEdit: { fields: ['name', 'email'] },
    linkToItem: true, // makes sure that the card links to fields in the user list
    inlineCreate: { fields: ['name', 'email'] },
},
/* segmented-control */
/* exposes all select values in the interface so editor's don't have to click the input in order to know which value to choose. Our limited set of draft/published options is a perfect fit for this! */
ui: {
    displayMode: 'segmented-control'
}
```

# config

```js
// The config function accepts an object representing all the configurable parts of the system:
export default config({
  lists: { /* ... */ },
  db: { /* ... */ },
  ui: { /* ... */ },
  server: { /* ... */ },
  session: { /* ... */ },
  graphql: { /* ... */ },
  extendGraphqlSchema: { /* ... */ },
  storage: { /* ... */ },
  experimental: { /* ... */ },
});
```

# config.storage

https://keystonejs.com/docs/apis/config#storage-images-and-files

```js
const storage = {
    my_local_images: {
        kind: 'local',
        generateUrl: path => `http://localhost:3000/images${path}`,
        serverRoute: { path: '/images', },
        storagePath: 'public/images',
    },
}

export default config({
    /* ... */
    storage
})
```



# SCHEMA API

```js
import { config, list } from '@keystone-6/core';

export default config({
  lists: ({
    ListName: list({
      fields: { /* ... */ },
      access: { /* ... */ },
      ui: { /* ... */ },
      hooks: { /* ... */ },
      graphql: { /* ... */ },
      db: { /* ... */ },
      description: '...',
      defaultIsFilterable: false,
      defaultIsOrderable: false,
    }),
    /* ... */
  }),
  /* ... */
});
```



## FIELDS API

```js
import { config, list } from '@keystone-6/core';
import {
  // Scalar types
  checkbox,
  integer,
  json,
  float,
  password,
  select,
  text,
  timestamp,

  // Relationship type
  relationship,

  // Virtual type
  virtual,

  // File types
  file,
  image,
} from '@keystone-6/core/fields';

// Complex types
import { document } from '@keystone-6/fields-document';
import { cloudinaryImage } from '@keystone-6/cloudinary';
```

### image

```js
import { config, list } from '@keystone-6/core';
import { image } from '@keystone-6/core/fields';

const lists = {
	MyImages: list({
      	fields: {
            images: image({ storage: 'my_image_storage' }), 
            // this needs to be configured in storage config
		},
	}),
}

```

