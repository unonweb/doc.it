# CACHE

"Once you have unique names you can use very aggressive caching headers too, which is great for performance."

## Netlify

* Static asset responses on Netlify are cached on Netlify’s global edge nodes and automatically invalidated whenever a deploy changes the content. 
* Static asset responses *can only change with new deploys*. 
  So, unless there is a new deploy or manual purge, we treat static asset responses as fresh for up to one year

## Cache Busting

Technique that web developers use to ensure that users always see the most up-to-date version of a website

* File name versioning
* File path versioning
* Query strings

The resources that work best with caching are static immutable files whose contents never change. And for resources that do change, it is a common best practice to change the URL each time the content changes, so that the URL unit can be cached for a longer period.

## HTTP headers

A web server tells a browser a few things about every single file it downloads:

- Can this be cached, and if so by whom?  (`cache-control: public` vs  `cache-control: private`)
- How long can it be cached for? (`cache-control: max-age=`)
- How can I tell if it’s been changed?  (`etag` in combination with above)

### netlify solution

```ini
cache-control: max-age=0, must-revalidate, public
```

*Please cache this content, and then do not trust your cache.* This seems a bit counterintuitive, but there’s a good reason. This  favors you as a content creator — you can change any of your content in  an instant. So your visitors don’t experience the dreaded new/old mixture that old, file-at-a-time deploy methods  left you with.

# COOKIES

A cookie doesn’t simply mean saving some piece of data in your browser.  It also means sending that piece of data in every request to the site  that created the cookie. This becomes pretty useful, for example for  authentication. With a cookie, you can store a token that identifies the user. Then, every time the browser makes a request to your site, it  will send the authentication token. Now, on your web server, you can  recognize users by their token (their cookie).

## httpOnly Cookie

* is **not accessible by the JavaScript**

  Only the browser knows about it, and it doesn’t give it to the JavaScript code in the page. At first, it might sound like a limitation, and it is. However, the goal of that is that we cannot trust the JavaScript code. An attacker may use JavaScript to *steal* our authentication  token stored in a cookie, and then access the website with our account.  With HttpOnly cookies, this is not possible. This makes **XSS attacks** harder to perform.

*It is a recognized best practice to share any authentication data only with HttpOnly cookies. Using a standard cookie for authentication is a known vulnerability we should avoid in any case.*

* storing session tokens

## HTTP

Cookies are usually set by a web-server using the response **`Set-Cookie`** HTTP-header. Then, the browser automatically adds them to (almost) every request to the same domain using the `Cookie` HTTP-header.

### server

```http
HTTP/2.0 200 OK
Content-Type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry

[page content]
```

### client

```http
GET /sample_page.html HTTP/2.0
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```

### lifetime

 of a cookie can be defined in two ways:

- ***Session* cookies** are deleted when the current session ends. The browser defines when the "current session" ends, and some browsers use *session restoring* when restarting. This can cause session cookies to last indefinitely.
- ***Permanent* cookies** are deleted at a date specified by the **`Expires`** attribute, or after a period of time specified by the `Max-Age` attribute.

```http
HTTP/2.0 200 OK
Content-Type: text/html
Set-Cookie: id=a3fWa; Expires=Thu, 31 Oct 2021 07:28:00 GMT;
```

### access

#### secure

```http
HTTP/2.0 200 OK
Content-Type: text/html
Set-Cookie: id=a3fWa; Expires=Thu, 21 Oct 2021 07:28:00 GMT; Secure; HttpOnly
```

The cookie should be transferred only over HTTPS.

By default, if we set a cookie at http://site.com, then it also appears at https://site.com and vice versa. 
That is, cookies are domain-based, they do not distinguish between the protocols.

A cookie with the **`Secure`** attribute is only sent to the server with an encrypted request over the HTTPS protocol. It's never sent with unsecured HTTP (except on localhost), which means *man-in-the-middle* attackers can't access it easily. However, don't assume that `Secure` prevents all access to sensitive information in cookies. For example, someone with access to the client's hard disk (or JavaScript if the `HttpOnly` attribute isn't set) can read and modify the information.

A cookie with the **`HttpOnly`** attribute is inaccessible to the JavaScript `document.cookie` API; it's only sent to the server. For example, cookies that persist in server-side sessions don't need to be available to JavaScript and  should have the `HttpOnly` attribute. This precaution helps mitigate XSS attacks.

### scope

The **`Domain`** attribute specifies which hosts can receive a cookie. 

* If the server does not specify a `Domain`, the browser defaults the domain to the same host that set the cookie, *excluding subdomains*. 
* If `Domain` *is* specified, then subdomains are always included. Therefore, specifying `Domain` is less restrictive than omitting it. However, it can be helpful when subdomains need to share information about a user.

The **`Path`** attribute indicates a URL path that must exist in the requested URL in order to send the `Cookie` header.  The `%x2F` ("/") character is considered a directory separator, and subdirectories match as well.

The **`SameSite`** attribute lets servers specify whether/when cookies are sent with cross-site requests. This provides some protection against cross-site request forgery attacks (CSRF). It takes three possible values: `Strict`, `Lax`, and `None`.

* `Strict`: the browser only sends the cookie with requests from the cookie's origin site
* `Lax` is similar, except the browser also sends the cookie when the user *navigates* to the cookie's origin site (even if the user is coming from a different site).  For example, by following a link from an external site.
* `None` specifies that cookies are sent on both originating and cross-site requests, but *only in secure contexts* (i.e., if `SameSite=None` then the `Secure` attribute must also be set).  If no `SameSite` attribute is set, the cookie is treated as `Lax`.

```http
HTTP/2.0 200 OK
Content-Type: text/html
Set-Cookie: myKey=myValue; Expires=Thu, 21 Oct 2021 07:28:00 GMT; Secure; HttpOnly; Domain=mozilla.org; SameSite=Strict
```

## JS

You can access existing cookies from JavaScript as well if the `HttpOnly` flag isn't set.

```js
document.cookie = "yummy_cookie=choco";
document.cookie = "tasty_cookie=strawberry";
console.log(document.cookie);
// logs "yummy_cookie=choco; tasty_cookie=strawberry"
```

## use cases

Cookies are mainly used for three purposes:

### session management

Logins, shopping carts, game scores, or anything else the server should remember

authentication:

1. Upon sign in, the server uses the `Set-Cookie` HTTP-header in the response to set a cookie with a unique “session identifier”.
2. Next time when the request is sent to the same domain, the browser sends the cookie over the net using the `Cookie` HTTP-header.
3. So the server knows who made the request.

### personalization

​    User preferences, themes, and other settings  

### tracking

Recording and analyzing user behavior  

# SECURITY
