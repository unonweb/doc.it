# WEB

* change localizations without needing to translate URLs

> We also decided that every localization of our site would live in a  subdirectory so one language isn’t elevated above another, and that all  URLs, except for the subdirectory, would be identical across  localizations based on the authoring language, allowing users to easily change localizations without needing to translate URLs. - https://www.smashingmagazine.com/2020/11/internationalization-localization-static-sites/

# HTML

## hreflang

(Das HTML-Attribut für mehrsprachige Websites)

* signalisiert den Crawlern, dass der Inhalt der aktuellen Seite in verschiedenen Sprachversionen verfügbar ist.

### bidirektional

Es ist essentiell zu verstehen, dass Hreflang-Tags als Gruppen  funktionieren. In anderen Worten, wenn du einen hreflang-Tag zu einer englischen Seite hinzufügst der zu einer italienischen Variante zeigt, dann **muss** die italienische Variante mit einem hreflang-Tag zurück auf die englische Seite zeigen.

Es verlinkt zwei oder mehrere Dokumente bidirektional und nicht – wie  bei einer Weiterleitung – unidirektional. Es reicht also nicht, wenn die deutschsprachige Website eine **hreflang-Verlinkung** auf  die spanischsprachige Variante enthält, diese aber nicht ebenso auf die  deutschsprachige Seite verweist. Nur wenn die hreflang-HTML-Anmerkung in allen Dokumenten in alle vorhandenen Richtungen (Sprachversionen)  gesetzt wird, kann die Suchmaschine die Struktur Ihres Webprojekts  erkennen und die Ergebnisse der suchenden Nutzer entsprechend anpassen. 

### selbstreferentiell

**jede Sprachversion muss sowohl sich selbst als auch alle anderen Sprachversionen auflisten**

```html
<head>
  <link rel="alternate" href="http://beispieldomain.de/" hreflang="de" />
	<link rel="alternate" href="http://beispieldomain.de/it/" hreflang="it" />
	<link rel="alternate" href="http://beispieldomain.de/es/" hreflang="es" />
	<link rel="alternate" href="http://beispieldomain.de/en/" hreflang="en" />
</head>
```

### google

Mithilfe von `hreflang` kannst du Google über Inhaltsvarianten informieren, damit wir erkennen können, dass es sich bei diesen Seiten um **lokalisierte Varianten derselben Inhalte** handelt. Google verwendet `hreflang` oder das HTML-Attribut `lang` nicht, um die Sprache einer Seite zu erkennen. Stattdessen nutzen wir dafür Algorithmen.    