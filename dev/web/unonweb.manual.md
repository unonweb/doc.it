# Anleitung: cms.unonweb.de

* **Version CMS**: 1.3
* **Version Manual**: 1.7
* **Adresse CMS**: [cms.unonweb.de:3000](https://cms.unonweb.de:3000)
* **Adresse Vorschau:** `<your-domain>.unonweb.de` z.B: `fridakahlo.unonweb.de`

# 1. Einführung

Hinweis: Einige der Begriffe und Abkürzungen, die hier verwendet werden, sind im Glossar am Ende dieses Benutzerhandbuchs aufgeführt.

## Setup & Workflow

### Setup

Am besten man öffnet im Browser **zwei Tabs**:

* Das CMS: [cms.unonweb.de:3000](https://cms.unonweb.de:3000)
* Die Vorschau-Website: `<your-domain>.unonweb.de`

### Workflow

* Im CMS können inhaltliche Änderungen insbesondere unter `Pages`, `Posts` oder `Sites` vorgenommen werden.
* **Speichern**: Die Änderungen müssen dann gespeichert werden (`Speichern`-Button oben rechts)
* **Tab-Wechsel** zur Vorschau-Webseite
* **Aktualisierung**: Veränderungen im CMS werden auf der Vorschau-Website erst sichtbar, wenn diese aktualisiert wurde 
  (F5-Taste oder CTRL + r)
* Evtl. weitere Änderungen auf anderen Seiten im CMS
* **Publish**: Am Ende der Sitzung - nachdem alle Änderungen vorgenommen und überprüft wurden - auf `Publish` klicken. Dann werden die gesammelten Änderungen auf die eigene Domain hochgeladen.

## Einstellungen

### 1.3 Benutzer-Einstellungen

Mit einem Klick auf das Benutzer-Icon ganz oben rechts gelangt man zu den Benutzer-Einstellungen. Dort kann unter anderem die Benutzeroberfläche konfiguriert werden.

* **Sprache**: Spracheinstellunge für die Benutzeroberfläche des CMS
* **Admin-Farbthema**: Light / Dark

## Sprachversionen der Website

In der Kollektion `Sites / Domains` können Spracheinstellungen vorgenommen werden, wie z.B. welche Sprachen für die Website verwendet werden.

Für alle Benutzer, die eine **mehrsprachige Website** erstellen: Bitte achtet stets darauf welche Sprachumgebung ihr aktuell ausgewählt habt. Der Schalter für die Sprachumgebung befindet sich neben dem Benutzer-Icon oben rechts. Alle inhaltlichen Veränderungen auf der aktuellen Webpage betreffen nur die ausgewählte Sprachversion.

# 2. Kurzanleitungen

## Neue Webpage erstellen

- Kollektion `Pages`
  - Button `Neu erstellen`

- Tab `Meta`
  - Titel vergeben

- Tab `Inhalt`
  - Im Editor lostippen oder mittels Schrägstrich `/` ein Menü zur Auswahl von anderen Inhalten aufrufen

- `Speichern` (rechts oben)

## Der Webpage ein Bild hinzufügen

- Kollektion `Pages`

- Tab `Inhalt`

  - Mittels Schrägstrich `/`  das Menü öffnen

  - `Upload` auswählen

  - Im neu geöffneten Menü sicherstellten, dass die Kollektion `Bilder` ausgewählt ist

  - Bild auswählen oder `Neu erstellen` 

  - Wenn das Bild im Editor eingefügt ist, können seine Eigenschaften per Klick auf folgendes Icon geändert werden:

    <svg style="height: 25px; width: 25px" class="icon icon--edit" viewBox="0 0 25 25" xmlns="http://www.w3.org/2000/svg"><polygon fill="white" class="fill" points="16.92 16.86 8.25 16.86 8.25 8.21 12.54 8.21 12.54 6.63 6.68 6.63 6.68 18.43 18.5 18.43 18.5 12.53 16.92 12.53 16.92 16.86"></polygon><polygon class="fill" fill="white" points="16.31 7.33 17.42 8.44 12.66 13.2 11.51 13.24 11.55 12.09 16.31 7.33"></polygon><rect class="fill" fill="white" height="1.15" transform="translate(10.16 -10.48) rotate(45)" width="1.58" x="16.94" y="6.44"></rect></svg>

## Das Layout der Webpage festlegen

Wenn kein spezifisches Layout definiert wird, werden alle Inhalte in einer Spalte angezeigt.

Um z.B. ein Bild neben eines Text in einem 2-spaltigen Layout anzuordnen,...

* Im Editor-Menü (`/`) eine Layout-Option (z.B. `Layout Flex`) auswählen
* `Spalte hinzufügen`
* Breite der Spalte (in Prozent des verfügaren Raums) festlegen

## Neue Seite der Navigation hinzufügen:

- Kollektion `Sites | Domains`
  - Domain auswählen

- Tab `Nav | Menü`
  - `Link hinzufügen` -> `Interner Link` -> neue Seite auswählen

- `Speichern` (rechts oben)

## Neuen Post erstellen

* Kollektion `Posts`
* Button `Neu erstellen`
* Post `Typ` auswählen (Post-Typen müssen vom Administrator eingerichtet werden)
* `Speichern`  -> daraufhin werden die für diesen Post-Typ verfügbaren Felder geladen
* Felder ausfüllen
* `Speichern`

## Posts auf einer bestehenden Webpage einbinden

- Kollektion `Pages`
- Tab `Inhalt` -> im Editor per `/` das Menü öffnen -> `Posts` auswählen -> `Post-Typ` auswählen

## Website veröffentlichen

In den Kollektionen Pages und Posts findet sich in der rechten Seitenleiste ein `Publish` Button. Dieser lädt die gesamte Website inkl. aller Änderungen auf allen Webpages auf die verknüpfte Domain hoch. 

> Vorher sollten über die Vorschau-Domain `<your-domain>.unonweb.de` geprüft werden ob alles wie gewünscht aussieht.

# 3. Kollektionen

## Pages

Auf `cms.unonweb.de` werden auf einer Webpage (Kollektion `Pages`) direkt nur die Meta-Daten (Tab `Meta`) der Page sowie der Hauptteil (Tab `Inhalt`) bearbeitet. Seitenübergreifende Elemente wie *Header* und *Navigation* werden unter `Sites | Domains` bearbeitet.

### Tab: Meta

* **URL**: Jede einzelne Webpage bekommt eine eigene URL (Adresse). Diese wird automatisch aus dem ersten Titel der Website erzeugt, kann jedoch über eine entsprechende Option angepasst werden. Achtung: Ist die Webseite einmal im Internet veröffentlich worden, wird davon abgeraten URLs nachträglich zu ändern.

### Tab: Inhalt

Hier können die Inhalte und das Layout der Webpage verändert werden.

## Posts

Auf `cms.unonweb.de` kann ein Post so verschiedenes wie ein klassischer Blog-Post, eine Veranstaltung, ein Rezept oder ein Produkt sein. Diese Flexibilität wird dadurch ermöglicht, dass ein Post einem bestimmten `Post-Typ` angehört. Dieser bestimmt welche Inhalte verfügbar sind, welche Struktur und welches Aussehen er hat. Aktuell können Post-Typen nur vom Administrator erstellt oder verändert werden.

Posts tauchen erst auf einer Webpage auf, wenn sie dort explizit eingebunden werden.

### Page vs. Post

Wann schreibe ich meine Inhalte direkt auf die Webpage und wann nehme ich den Umweg über einen Post?

* **Page**: statische Inhalte, die eine feste Adresse haben
  * Vorteil: Einfach

  * Nachteil: Inhalte sind mit der Page fest verknüpft

  * Beispiel: "Über mich"-Seite

* **Post**: Inhalte mit Wiederholungscharakter, die dynamisch an verschiedenen Stellen auftauchen. 
  * Vorteil: Höhere Flexibilität
  * Nachteil: Aktuell können neue Post-Typen nur von unonweb erstellt werden
  * Beispiel: Blog-Post, Rezept, Veranstaltung

## Upload

### Bilder

* **Format**: Bilder werden beim Upload in das Format *.webp* konvertiert. Falls die erzielt Qualität nicht zufriedenstellend ist, wenden Sie sich bitte an support@unonweb.de
* Ein Bild kann nur von dem Benutzer, der es hochgeladen hat, verändert werden.
* Es tauchen in allen Kollektion Bilder auf, die von *unonweb* als Beispiele und zur freien Verwendung hochgeladen wurden. Falls diese die Übersicht stören, können sie in der Listenansicht der Kollektion mithilfe von `Erstellt von Benutzer` herausgefiltert werden.

### Dokumente

* **Format**: Hier werden aktuell nur .pdf-Dateien zum Upload akzeptiert.

# 4. Anhang

## Glossar

* ***CMS*** : Content Management System
* ***Collection* / *Kollektion*** : Sammlung von Inhalten im CMS eines bestimmten Typs
* ***Webpage* vs. *Website*** : Eine Webpage ist eine einzelne Seite einer Website 
  Website = Buch
  Webpage = Seite im Buch
  Dementsprechend: Webseite = Webpage
* ***Homepage* vs. *Subpage*** : Das ist die Webpage auf der man landet, wenn nur die Domain eingegeben wird
  Homepage: `www.beispiel.de`
  Subpage: `www.beispiel.de/shop`
