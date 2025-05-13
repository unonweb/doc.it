Wikimedia
------------------------------------

* a RESTful web service

### request format

#### endpoints

All Wikimedia wikis have endpoints that follow this pattern: 
* `https://www.example.org/w/api.php`
* `https://en.wikipedia.org/w/api.php`

#### query string parameters
* `?`
* `action=query` 	# Fetch data from and about MediaWiki
	Get list of pages matching a certain criterion:
	* `list=`
	Get properties of pages:
	* `prop=categories` 	# List all categories the pages belong to.
	Get meta information about the wiki and user
	* `meta=`
* `format=json` 	# Output data in JSON format.
* `format=jsonfm` 	# Output data in JSON format (pretty-print in HTML).
* `origin=`
When accessing the API using a cross-domain AJAX request (CORS), set this to the originating domain. This must be included in any pre-flight request, and therefore must be part of the request URI (not the POST body).  

For authenticated requests, this must match one of the origins in the Origin header exactly, so it has to be set to something like https://en.wikipedia.org or https://meta.wikimedia.org. If this parameter does not match the Origin header, a 403 response will be returned. If this parameter matches the Origin header and the origin is whitelisted, the Access-Control-Allow-Origin and Access-Control-Allow-Credentials headers will be set.

For non-authenticated requests, specify the value *. This will cause the Access-Control-Allow-Origin header to be set, but Access-Control-Allow-Credentials will be false and all user-specific data will be restricted.

### Wikipedia

curl -X GET "https://de.wikipedia.org/api/rest_v1/page/summary/Heller_Tigerpython"

### Wiktionary

Using the API to extract a given page is usually a multi-step affair: request the list of revisions for an article title, then request the content of the title with a specific revision. The contents are usually delivered as an html blob in a json structure, and will need to be traversed to extract the specific data you are looking for. XSLT may be useful where articles are properly formatted, but keep in mind Wiktionary articles are manually edited bodies of text - not strictly formatted data files. It is possible to abstract specific sections via the API, but in most cases it will involve at least another connection to the API and so may in practice be slower than performing that parsing locally.




Dictionaries
---------------------------------------------------

### DWDS

https://www.dwds.de/d/api

### Wiktionary

supports the standard MediaWiki API - this basically lets you return page markup in a somewhat parseable format. 

### Owlbot

curl --header "Authorization: Token a538a3211dc031d3847a2c70484fc8b32ab0d9eb" https://owlbot.info/api/v4/dictionary/owl -s | json_pp

### Youtube

AIzaSyA1rSHD6Hg9E0zIBXcvEOE6tNyYAHD8GtQ

```shell
curl \
	'https://youtube.googleapis.com/youtube/v3/search?key=[YOUR_API_KEY]' \
	--header 'Authorization: Bearer [YOUR_ACCESS_TOKEN]' \
	--header 'Accept: application/json' \
	--compressed

#  Eolk - Dark Forest

curl 'https://youtube.googleapis.com/youtube/v3/search?part=%20Eolk%20-%20Dark%20Forest&key=AIzaSyA1rSHD6Hg9E0zIBXcvEOE6tNyYAHD8GtQ' \
	--header 'Authorization: Bearer [YOUR_ACCESS_TOKEN]' \
	--header 'Accept: application/json' \
	--compressed
```