# PERFORMANCE

http2

1. HTTP 2.0 **Multiplexing* and Concurrency (allows multiple resources to be requested on a single TCP connection)
2. HTTP 2.0 **Server Push** (Server push allows the server to preemptively push the responses it thinks the client will need into the client's cache)

## http 1.1 vs http 2

> A typical web page has 30+ resources to download in order to be rendered. With HTTP/1.1, a browser opens 6 connections to the server, request 6 resources in parallel, wait for those to be downloaded, then request  other 6 resources and so forth (or course some resource will be  downloaded faster than others and that connection could be reused before than others for another request). The point being that with HTTP/1.1 you can only have at most 6  outstanding requests.
>
> To download 30 resources you would need 5 roundtrips, which adds a lot of latency to the page rendering.
>
> In order to make the page rendering faster, with HTTP/1.1 the  application developer had to reduce the number of requests for a single  page. This lead to "best practices" such as domain sharding, resource  inlining, image spriting, resource bundling, etc., but these are in fact just clever hacks to workaround HTTP/1.1 protocol limitations.
>
> With HTTP/2 things are different because HTTP/2 is multiplexed. Even without HTTP/2 Push, the multiplexing feature of HTTP/2 renders all those hacks useless, because now you can request hundreds of resources  in parallel using a single TCP connection.
>
> With HTTP/2, the same 30 resources would require just 1 roundtrip to  be downloaded, giving you a straight 5x performance increase in that operation (that typically dominates the page rendering time).
>
> Given that the trend of web content is to be richer, it will have  more resources; the more resources, the better HTTP/2 will perform with  respect to HTTP/1.1.
>
> On top of HTTP/2 multiplexing, you have HTTP/2 Push.
>
> Without HTTP/2 Push, the browser has to request the primary resource  (the *.html page), download it, parse it, and then arrange to download  the 30 resources referenced by the primary resource.
>
> HTTP/2 Push allows you to get the 30 resources while you are  requesting the primary resource that references them, saving one more  roundtrip, again thanks to the HTTP/2 multiplexing.
>
> It is really the multiplexing feature of HTTP/2 that allows you to forget about resource bundling.
>
> https://stackoverflow.com/questions/30861591/why-bundle-optimizations-are-no-longer-a-concern-in-http-2

## JS

Unlike the image file, for example, which only needs to be rendered  once the file has been downloaded, JavaScript files need to be **parsed, compiled, and executed**. This is a CPU-intensive operation that **blocks the main thread** making the page **unresponsive** for that time. A user **cannot interact** with the page during that phase even though the content might be  displayed and has seemingly finished loading. If the script takes too  long to parse and execute, the user will get the impression that the  site is broken and leave. This is why Lighthouse and Core Web Vitals  specify First Input Delay (FID) and Total Blocking Time (TBT) metrics to measure site interactivity and input responsiveness.

JavaScript is also a **render-blocking resource**, meaning that if the browser encounters a script within the HTML  document which isn’t deferred, it doesn’t render the page until it loads and executes the script. HTML attributes `async` and `defer` signal to the browser not to block page processing, however, **the CPU thread still gets blocked** and the script needs to be executed before the page becomes responsive to user input.

By default all JavaScript is parser blocking. Because the browser does  not know what the script is planning to do on the page, it assumes the  worst case scenario and blocks the parser. A signal to the browser that  the script does not need to be executed at the exact point where it's  referenced allows the browser to continue to construct the DOM and let  the script execute when it is ready; for example, after the file is  fetched from cache or a remote server.

## bundling

### opinions

[The Right Way to Bundle Your Assets for Faster Sites over HTTP/2](https://medium.com/@asyncmax/the-right-way-to-bundle-your-assets-for-faster-sites-over-http-2-437c37efe3ff)

> In the HTTP/1.1 era, there was no question that concatenating multiple assets into one big file is the most important optimization for increasing loading performance of web applications.
>
> One downside of this approach was a negative impact on browser’s cache management. If one small asset changes, the whole big file has to be  downloaded again. But the performance gain from the concatenation simply outweighs this penalty in HTTP/1.1 environment.

* **Concatenate files into several bundles**
  Even though HTTP/2 is designed to be highly efficient in transferring many small files, tiny overhead of each small file can add up and becomes noticeable when we are dealing with many files.
* **Create smaller bundles instead of one gigantic bundle**. Each bundle should have a set of related asset files.
  If a bundle consists of related assets, most changes in a group can be contained locally without impacting other groups (enhance browser’s cache performance)

[Forgo JS packaging? Not so fast](https://blog.khanacademy.org/forgo-js-packaging-not-so-fast/)

* **Bundling improves compression**
  In particular, it will always compress 100 1K files worse (in aggregate) than the single 100K file
  (More details: at a high level, zlib compresses like this: it goes through a document, and for every sequence of text it’s looking at, it sees if that text had occurred previously in the document. If so, it replaces that sequence of text by a (space-efficient) pointer to that previous occurrence. It stands to reason that the further along in the  document it goes, the more “previous text” there is for finding a potential match and thus an opportunity for compression.

### strategies

| Approach          | Pros                                | Cons                                   |
| ----------------- | ----------------------------------- | -------------------------------------- |
| Package per page  | No unused files Only 1 HTTP request | Poor caching                           |
| Separate JS files | No unused files Optimal caching     | Many HTTP requests Limited parallelism |
| Hybrid            | Few HTTP requests OK caching        | Some unused files OK caching           |

