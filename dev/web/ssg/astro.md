# Runtime API

### getStaticPaths()

* should return an *array of objects* to determine which paths will be pre-rendered by Astro
* The `params` key of every returned object tells Astro what routes to build. 
  The returned *params* must map back to the dynamic parameters and rest parameters defined in your component filepath.
* To pass additional data to each generated page, you can also set a `props` key on every returned path object

#### ex

* only params

```js
---
export async function getStaticPaths() {
  return [
    { params: { id: '1' } },
    { params: { id: '2' } },
    { params: { id: '3' } }
  ];
}

const { id } = Astro.params;
---
<h1>{id}</h1>
```

* params and props

```js
---
export async function getStaticPaths() {
  const posts = await fetch('...').then(response => response.json());
	// or 
  const posts = [
    {id: '1', category: "astro", title: "API Reference"},
    {id: '2', category: "react", title: "Creating a React Counter!"}
  ];
  return posts.map((post) => {
    return {
      params: { id: post.id },
      props: { post },
    };
  });
}

const { id } = Astro.params;
const { post } = Astro.props;
---
<h1>{id}: {post.name}</h1>
```



#### ex: payload-astro

```js
---
export async function getStaticPaths() {
    const posts = await getPosts();
    const paths = posts.docs.map((post) => {
        return {
            params: { slug: post.slug },
            props: { post },
        };
    });
    return paths;
}
const { post } = Astro.props;
const content = post.content;
---
<main>
  <h1>{post.title}</h1>
	{post.content && <RichText richText={content} />}
</main>
```

