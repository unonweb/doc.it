# notes

## validation

* a custom validation function must return either `true` or a `string`
* `beforeChange` seems to run even if server-side validation fails
* `afterChange` runs only after successful server-side validation

## defaultValue

* client-side fetch only works with an authenticated user

```js
{
	defaultValue: async ({ user }) => {
    // user is undefined in internal operations
    if (user) {
    	const res = await fetch('/api/headers')
      if (res.ok) {
        const data = await res.json()
        if (data?.docs[0]?.id) {
          return data.docs[0].id
        } else {
          return null
        }
      }
    }
  }
}
```



## queries

* query versions: 
  * the `page.id`  in a draft becomes `parent.id`
  * with versions enabled it's currently (1.15.6) not possible to query the `parent` field

## hooks

### beforeValidate

* changes to returned data are saved to database

beforeChange

* `id` is not available on creation

### afterChange

* `id` is available on creation

## hooks arguments

* fields hooks `args.value` is not available on doc creation

# react

```js
const field = {
  name: 'attribution',
  type: 'text',
  defaultValue: ({ user, locale }) => (`${translation[locale]} ${user.name}`)
  admin: {
  	description: ({ value }) =>
    	(`${typeof value === 'string' ? 20 - value.length : '20'} characters left`)
	}
}

// mine
admin: {
    description: (args) => {
    	console.log(args)
				return (
					<span>Only WOFF2 format accepted. <br></br> <a href="https://www.fontsquirrel.com/">Get Fonts</a></span>
        )
    }	
},
```

## useFormFields

```js
const domainShort = useFormFields(([fields]) => {
		return fields.domainShort.value
	});
```



# queries

## where

* with versions enabled it's currently (1.15.6) not possible to query the `parent` field

```js
const whereQuery = {
	and: [
  	{
    	sites: { contains: siteID } // seems not to work as sites is an array
    },
    {
      id: { not_equals: req.user.id }
    }
  ]
}

const query = {
  or: [
    {
      allSites: { equals: true, },
    },
    {
      sites: { contains: "652726f0a9995afbbd192649", },
    },
  ],
}
```

### operators

```js
// either the color is mint 
// OR the color is white AND featured is set to false.

const query = {
  or: [ // array of OR conditions
    {
      color: {
        equals: 'mint',
      },
    },
    {
      and: [ // nested array of AND conditions
        {
          color: { equals: 'white' }
        },
        {
          featured: { equals: false }
        }
      ]
    }
  ]
}
```

```js
// or
const where = {
  or: 
		[
			{
				[siteIDFieldName]: { // [] needed because you use a variable as an obj key 
					in: user.sites
				}
			},
			{
				[siteIDFieldName]: {
					exists: false,
				}
			}
		]
}
```

```js
'http://localhost:3000/api/pages?depth=0&where[site][equals]=63e26e0ec3b8d9894be7bd46' 
// depth=0 makes sure that relationships are presented as id
// where[site][equals]=63e26e0ec3b8d9894be7bd46 filters on the given site id
```

## nested properties

```js
const whereMdieterichSite = {
	site: {
		id: {
			equals: '63e26e0ec3b8d9894be7bd46',
		}
	},
}
```

## local

```js
const result = await payload.find({
    collection: 'categories',
    where: {
        id: {
            in: ['619bea3fcc9ad5dd9692139f', '619bea2ccc9ad5dd96921377']
        },
    }
})
```

## rest

```ini
http://localhost:3000/api/access
```

### apiKey

```js
import User from '../collections/User';

const response = await fetch("http://localhost:3000/api/pages", {
  headers: {
    Authorization: `${User.slug} API-Key ${YOUR_API_KEY}`, 
    // The header is case-sensitive and needs the slug of the auth.useAPIKey enabled collection
  },
})

async function queryWithApiKey(url, authColSlug = 'users', apiKey) {
    if (!token) throw new Error('token required')
    // use the collection slug
    let res = await fetch(url, {
        headers: {
					Authorization: `${authColSlug} API-Key ${apiKey}`,
		},
    })
		let resBody = await res.json()
    console.dir(resBody)
    return resBody
}
```

### getPosts()

```js
async function queryWithToken(url, token) {
  if (!token) throw new Error('token required')
  let authorization = `JWT ${token}`
	let res = await fetch(url, {
  	headers: { Authorization: authorization	},
  })
	let resBody = await res.json()
	return resBody
}

/* getToken */

async function getToken(email, pw) {
	const res = await fetch('http://localhost:3000/api/users/login', {
		method: 'POST',
		headers: {
			'Content-Type': 'application/json',
		},
		body: JSON.stringify({
			email: email,
			password: pw,
  		})
	})
	const resBody = await res.json()
	//console.dir(resBody)
	return resBody.token
}
```

# custom components

```js
import Button from 'payload/dist/admin/components/elements/Button';

export const Pages = {
	slug: 'pages',
	admin: {
		components: {
			AfterList: [
				() => <Button label='custom' />,
				() => <div>My Edit</div>,
			]
		},
	},
}
```

```js
// customComponent.js
import * as React from "react";

const customComponent = () => {
	return <div>Some text</div>;
};
  
export default customComponent;
```

```js
import customComponent from './src/custom/customComponent.js'

export default buildConfig({
	serverURL: 'http://localhost:3000',
	admin: {
    components: {
			afterNavLinks: [
				customComponent
			]
		},
  }
}
```



# examples

## user rating

```js
const ProductsCollection: CollectionConfig = {
  slug: 'products-for-rating',
  labels: { plural: 'Products' },
  admin: {
    group: 'Rating',
    useAsTitle: 'productName'
  },
  fields: [
    {
      name: 'productName',
      type: 'text',
    }
  ],
}
```

```js
const RatingsCollection: CollectionConfig = {
  slug: 'ratings',
  admin: {
    group: 'Rating',
  },
  fields: [
    {
      name: 'product',
      type: 'relationship',
      relationTo: 'products-for-rating',
      validate: async (val, { user }) => {
        const query = qs.stringify({
          where: {
            and: [
              {
                product: { equals: val }
              },
              {
                user: { equals: user.id }
              }
            ]
          } 
        }, { addQueryPrefix: true })
        
        const response = await fetch(`http://localhost:3000/api/ratings${query}`)
        const sameRating = await response.json()

        if (!sameRating.totalDocs) {
          return true;
        }

        return 'You have already rated this product.';
      },
    },
    {
      name: 'rating',
      type: 'number',
      min: 1, max: 5,
      // admin: {
      //   components: {
      //     Field: FiveStarsField
      //   }
      // }
    },
    {
      name: 'user',
      type: 'relationship',
      relationTo: 'users',
      admin: {
        readOnly: true
      },
    },
  ],
  access: {
    read: ({ req: { user }, id }) => { 
      return {
        user: { 
          equals: user.id 
        }
      } 
    }
  },
  hooks: {
    beforeChange: [async ({
      data,
      req, 
    }) => {
      data.user = req.user.id
      return data;
    }]
  }
}
```



# config

```js
serverURL: 'http://localhost:3000', 
// A string used to define the absolute URL of your app including the protocol. Only protocol, domain and (optionally) port
admin: {}, // Base Payload admin configuration. Specify custom components, control metadata, set the Admin user collection, and more.
collections: [ ] // An array of all Collections that Payload will manage.
globals: [], // An array of all Globals that Payload will manage
plugins: [], // An array of Payload plugins.
routes: {}, // Control the routing structure that Payload binds itself to. Specify admin, api, graphQL, and graphQLPlayground.
express: {}, // Express-specific middleware options such as compression and JSON parsing.
graphQL: {}, // Manage GraphQL-specific functionality here. Define your own queries and mutations, manage query complexity limits, and more.
debug: false, // Enable to expose more detailed error information.
localization: {}, // Opt-in and control how Payload handles the translation of your content into multiple locales
hooks: {}, // Tap into Payload-wide hooks
upload: {},
maxDepth: 10, // The maximum allowed depth to be permitted application-wide. This setting helps prevent against malicious queries. Defaults to 10.
defaultDepth: 2, // If a user does not specify depth while requesting a resource, this depth will be used. 
cookiePrefix: 'payload', // A string that will be prefixed to all cookies that Payload sets.
cors: [], // Either a whitelist array of URLS to allow CORS requests from, or a wildcard string ('*') to accept incoming requests from any domain.
csrf: [], // A whitelist array of URLs to allow Payload cookies to be accepted from as a form of CSRF protection.
indexSortableFields: true, // Automatically index all sortable top-level fields in the database to improve sort performance and add database compatibility for Azure Cosmos and similar.
rateLimit: {} // Control IP-based rate limiting for all Payload resources. Used to prevent DDoS attacks and more.
upload: {} // Base Payload upload configuration. More.
```

## config.globals

Global configs are in many ways similar to Collections. The big difference is that Collections will potentially contain many documents, while a Global is a "one-off". Globals are perfect for things like header nav, site-wide banner alerts, app-wide localized strings, and other "global" data that your site or app might rely on.

As with Collection configs, it's often best practice to write your Globals in separate files and then import them into the main Payload config.

```js
slug: '', // Required. Unique, URL-friendly string that will act as an identifier for this Global.
fields = [], // Required. Array of field types that will determine the structure and functionality of the data stored within this Global. Click here for a full list of field types as well as how to configure them.
    
label: '', // Singular label for use in identifying this Global throughout Payload. Auto-generated from slug if not defined.
description: '', //	Text or React component to display below the Global header to give editors more information.
admin: {}, // Admin-specific configuration.
hooks: {}, // Entry points to "tie in" to collection actions at specific points. More
access: {}, // Provide access control functions to define exactly who should be able to do what with this Global.
versions: true, // Set to true to enable default options, or configure with object properties. More
```

## config.localization

```js
// ...
localization: {
    locales: ['en', 'es', 'de', ],
    defaultLocale: 'en',
	fallback: true,
},
// ...
```

## config.admin

```js
// To specify which Collection to use to log in to the Admin panel, pass the admin options a user key equal to the slug of the Collection that you'd like to use:
const config = buildConfig({
  admin: {
    user: 'admins', 
  },
})
```

## config.collections

```js
slug: 'categories', // Required. Unique, URL-friendly string that will act as an identifier for this Collection.
fields: [], // Required. Array of field types that will determine the structure and functionality of the data stored within this Collection. Click here for a full list of field types as well as how to configure them.
labels: {
    singular: 'All Fields',
    plural: 'All Fields',
}, // Singular and plural labels for use in identifying this Collection throughout Payload. Auto-generated from slug if not defined.
description: '', // Text or React component to display below the Collection label in the List view to give editors more information.
admin: {}, // Admin-specific configuration
hooks: {}, // Entry points to "tie in" to Collection actions at specific points.
access: {}, // Provide access control functions to define exactly who should be able to do what with Documents in this Collection.
auth: true, // Specify options if you would like this Collection to feature authentication.
upload: {}, // Specify options if you would like this Collection to support file uploads.
timestamps: true, //	Set to false to disable documents' automatically generated createdAt and updatedAt timestamps.
versions: true, //	Set to true to enable default options, or configure with object properties.
```

## config.collections.admin

```js
useAsTitle: '', //	Specify a top-level field to use for a document title throughout the Admin panel. If no field is defined, the ID of the document is used as the title.
defaultColumns: [], // Array of field names that correspond to which columns to show by default in this collection's List view.
disableDuplicate: true, // Disables the "Duplicate" button while editing documents within this collection.
enableRichTextRelationship: true, // The Rich Text field features a Relationship element which allows for users to automatically reference related documents within their rich text. Set to true by default.
preview: // Function to generate preview URLS within the Admin panel that can point to your app. More.
components: // Swap in your own React components to be used within this collection. More
```



## config.collections.fields

Fields are the building blocks of Payload. Collections and Globals both use Fields to define the shape of the data that they store.

```js
type: 'array' // for repeating content, supports nested fields
type: 'blocks' // block-based fields, allowing powerful layout creation
type: 'checkbox' // boolean true / false checkbox
type: 'code' // code editor that saves a string to the database
type: 'date' // date / time field that saves a timestamp
type: 'email' // validates the entry is a properly formatted email
type: 'group' // nest fields within an object
type: 'number' // field that enforces that its value be a number
type: 'point' // geometric coordinates for location data
type: 'radio' // radio button group, allowing only one value to be selected
type: 'relationship' // assign relationships to other collections
type: 'richtext' // fully extensible Rich Text editor
type: 'row' // used for admin field layout, no effect on data shape
type: 'select' // dropdown / picklist style value selector
type: 'text' // simple text input
type: 'textarea' // allows a bit larger of a text editor
type: 'upload' // allows local file and image upload
type: 'ui' // inject your own custom components and do whatever you need
```

## config.collections.fields.upload

```js
// Every Payload Collection can opt-in to supporting Uploads by specifying...
upload: true
upload: {} // an object containing `upload` options
```



```js
name: '', // Required. To be used as the property name when stored and retrieved from the database.
relationTo: '', // Required. Provide a single collection slug to allow this field to accept a relation to. Note: the related collection must be configured to support Uploads.
   
filterOptions: // A query to filter which options appear in the UI and validate against.
maxDepth: // Sets a number limit on iterations of related documents to populate when queried.
label: '', // Used as a field label in the Admin panel and to name the generated GraphQL type.
unique: true, // Enforce that each entry in the Collection has a unique value for this field.
validate: // Provide a custom validation function that will be executed on both the Admin panel and the backend.
index: // Build a MongoDB index for this field to produce faster queries. Set this field to true if your users will perform queries on this field's data often.
saveToJWT // If this field is top-level and nested in a config supporting Authentication, include its data in the user JWT.
hooks: // Provide field-based hooks to control logic for this field.
access: // Provide field-based access control to denote what users can see and do with this field's data. 
hidden: true, // Restrict this field's visibility from all APIs entirely. Will still be saved to the database, but will not appear in any API or the Admin panel.
defaultValue: // Provide data to be used for this field's default value. More
localized: true // Enable localization for this field. Requires localization to be enabled in the Base config.
required: true // Require this field to have a value.
admin: {}, // Admin-specific configuration. See the default field admin config for more details.
```

## config.collections.fields.admin

```js
condition: // You can programmatically show / hide fields based on what other fields are doing.
components:	// All field components can be completely and easily swapped out for custom components that you define.
description: '', //	Helper text to display with the field to provide more information for the editor user.
position: 'sidebar'	// Specify if the field should be rendered in the sidebar
width: '', // Restrict the width of a field. you can pass any string-based value here, be it pixels, percentages, etc. This property is especially useful when fields are nested within a Row type where they can be organized horizontally.
style: '', // Attach raw CSS style properties to the root DOM element of a field.
className: '', // Attach a CSS class name to the root DOM element of a field.
readOnly: true, // Setting a field to readOnly has no effect on the API whatsoever but disables the admin component's editability to prevent editors from modifying the field's value.
disabled: true, // If a field is disabled, it is completely omitted from the Admin panel.
hidden: true, // Setting a field's hidden property on its admin config will transform it into a hidden input type. Its value will still submit with the Admin panel's requests, but the field itself will not be visible to editors.
```

## config.collections.hooks

All collection Hook properties accept arrays of synchronous or  asynchronous functions. 
Each Hook type receives specific arguments and has the ability to modify specific outputs.

```js
const {afterChangeHook} = require('../../hooks.js')

hooks: {
    // must be an array
    // arguments are passed automatically
	beforeOperation: [(args) => {...}],
    beforeValidate: [(args) => {...}],
    beforeDelete: [(args) => {...}],
    beforeChange: [(args) => {...}],
    beforeRead: [(args) => {...}],
    afterChange: [afterChangeHook],  // runs after a document is created or updated
    afterRead: [(args) => {...}],
    afterDelete: [(args) => {...}],

    // Auth-enabled hooks
    afterLogin: [(args) => {...}],
    afterForgotPassword: [(args) => {...}],
}
                          
async function afterChangeHook({
	doc, // full document data
	req, // full express request
    operation, // name of the operation ie. 'create', 'update'
}) => {
  return doc
}
```



## access

You can manage access within Payload on three different levels:

* Collections
* Fields
* Globals

## payload.init()

```js
email: {}, // Base email settings to allow Payload to generate email such as Forgot Password requests and other requirements.
```

