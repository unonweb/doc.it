- https://daniel.haxx.se/docs/curl-vs-wget.html
# CURL

übertragt Dateien von oder zu einem Server; unterstützte sehr viele Netzwerkprotokolle; powered by libcurl; designed to work without user interaction.

* supported protocols: 
  DICT, FILE, FTP, FTPS, GOPHER, HTTP, HTTPS, IMAP, IMAPS, LDAP, LDAPS, POP3, POP3S, RTMP, RTSP, SCP, SFTP, SMB, SMBS, SMTP, SMTPS, TELNET, TFTP
  (wget only supports HTTP, FTP)
* bidirectional: 
  curl offers upload and sending capabilities. Wget only offers plain HTTP POST support.
* concurrency:
  curl can do many transfers in parallel (-Z). wget only does serial.
* eher wie cat
* no recursive downloading
* HTML parser
* Bidirectional: upload and sending capabilities

# CLI

```shell
curl http://www.dataden.tech 			# invokes an HTTP GET method on the given URL
curl www.dataden.tech > output.html 	# saves the output into a file

curl --url "url" # may be omitted
curl -X GET --url "url"

curl -w "%{time_total}\n" -o /dev/null -s www.example.com 
# outputs the total time it took to fetch the response

curl --user-agent "Mozilla/4.73 [en] (X11; U; Linux 2.2.15 i686)" www.example.com
```

## download file

```sh
curl --location --remote-name https://github.com/coreruleset/nextcloud-rule-exclusions-plugin/archive/refs/tags/v1.2.0.zip

curl --remote-name <url> # save file with the same name as on the server you’re downloading from; curl -O
curl --output <name> <url> # specify new filename; curl -o
curl --location # (HTTP) If the server reports that the requested page has moved to a different location (indicated with a Location: header and a 3XX response code), this option makes curl redo the request on the new place; curl -L
```

## method

```sh
# set method:
curl -X POST|GET|HEAD
curl --request POST|GET|HEAD
```

## headers

```sh
# get only the response headers:
curl --head # (HTTP  FTP FILE) Fetch the headers only! HTTP-servers feature the command HEAD which this uses to get nothing but the header of a document. When used on an FTP or FILE file, curl displays the file size and last modification time only; curl -I

curl --header "X-Secret: 4444e253e6ce16142757f3f7c63fcd73cf2a5f4fe51bffd03235f2fe0d3685fb" --url "https://api.pons.com/v1/dictionary?l=deen&q=house" --head
```

```sh
# set request headers:
curl --header "header" # (HTTP IMAP SMTP) Extra header to include in information sent. When used within an HTTP request, it is added to the regular request headers; curl -H
```

## post

```sh
# changes to POST automatically
curl -d
curl --data "data" 

# post a json 
curl --request POST --url "http://localhost:5000/api/user" --header 'content-type: application/json' --data '{"name":"Arrow","description":"bad movie","rating":"7.0","image":"michaeljackson.png"}'
# post data from the query string
curl --request POST --url "http://www.dataden.tech" --data "name=John&surname=Doe"
```

## examples

```shell
# make one hundred requests, numbered in the query string
for N in {1..100}; do 
	curl --silent --insecure https://localhost/index.html?n=${N}a >/dev/null
done

# make one hundred requests. But this time we want to send the data using a POST request in the body of the request. We are dynamically creating this payload in such a way that it gets bigger every time it is called. We use uuidgen to generate the data we need. This is a command that generates an ascii ID. Stringed together, we get a lot of data. (If there is an error message, this could be because the uuidgen command is not present. In this case, the uuid package should be installed).
for N in {1..100}; do 
	PAYLOAD=$(uuid -n $N | xargs)
	curl --silent --data "payload=$PAYLOAD" --insecure https://localhost/index.html?n=${N}b >/dev/null
done
```
