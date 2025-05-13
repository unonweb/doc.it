# HTTP Basic authentication

HTTP Basic authentication (BA) implementation is the simplest technique for enforcing access controls to web resources because it does not require cookies, session identifiers, or login pages; rather, HTTP Basic authentication uses standard fields in the HTTP header. 

## server

```http
HTTP 401 Unauthorized
WWW-Authenticate: Basic realm="User Visible Realm", charset="UTF-8" 
```

## client

* credentials are merely encoded with Base64 in transit and not encrypted or hashed in any way
* Therefore, basic authentication is typically used in conjunction with HTTPS to provide confidentiality. 
* In modern browsers, cached credentials for basic authentication are typically cleared when clearing browsing history

```http
GET /hello.html HTTP/1.1
Host: www.hello.com
Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ== 
```

* Base64 encoding of `Aladdin:open sesame`

# bearer token

* https://www.predic8.de/bearer-token-autorisierung-api-security.htm

**Was ist ein Token?**

Ein Token kann von einer Partei an eine  andere übergeben werden. Ein Personalausweis ist ein Token, das die  Stadt an einen Bürger übergibt.

**Was sind Bearer Token?**

Die Gültigkeit eines Ausweises ist auf eine Person beschränkt. Bei einer  Polizeikontrolle muss stets der eigene Ausweis vorgezeigt werden. Es  gibt Tokens, die dieser Beschränkung nicht unterliegen. Ein Schlüssel  kann einer Werkstatt übergeben werden, damit diese ein Auto entsperren  können. Es genügt, im Besitz des Schlüssels zu sein, um die Türe zu  öffnen. Der Begriff *Bearer* bedeutet auf Deutsch Träger oder  Inhaber. Ein Bearer Token ist ein Inhaber-Token, das nicht an eine  bestimmte Identität gebunden ist.

**Token in der IT Sicherheit**

In der IT Sicherheit werden Token für die *Authentifizierung* und *Autorisierung* von Benutzern und Systemen verwendet. Neben Hardware-Token wie z.B. Kryptokarten und *Pin Generatoren* können auch Daten als Token verwendet werden.

Die folgenden Token enthalten selbst sinnvolle Daten für den Empfänger bereit:

- JSON Web Token (JWT)
- X.509 Zertifikate
- Passwort Hashes

Des Weiteren gibt es Token, die als Stellvertreter dienen, z.B. für sensible Zugangsdaten, die auch *Credentials* genannt werden. Der Inhalt oder Wert eines Stellvertreter-Tokens hat selbst keine Bedeutung. Er ist nicht *self-contained*. Um ein Stellvertreter-Token zu schützen wird als Wert z.B. eine große Zufallszahl verwendet, die nicht leicht zu erraten ist.