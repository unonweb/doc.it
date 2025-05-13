
### namespace

`xmlns:prefix="URI"`

* element namespaces are inherited from the parent element

#### Namespace URI
* The namespace URI is not used by the parser to look up information.
* The purpose of using an URI is to give the namespace a unique name.
* However, companies often use the namespace as a pointer to a web page containing namespace information.
* The actual namespace itself, often a IRI, is of no real consequence. It should be unique, so people tend to choose a IRI/URI that they own, but it has no greater meaning than that. Sometimes people will place the schema (definition) for the XML at the specified IRI, but that is a convention of some people only.

#### Prefix
The prefix is of no consequence either. The only thing that matters is what namespace the prefix is defined as. Several tags beginning with different prefixes, all of which map to the same namespace are considered to be the same.

#### Default namespace
Defining a default namespace for an element saves us from using prefixes in all the child elements. It has the following syntax:
`xmlns="namespaceURI"`

If you declare a namespace without the identifier, (`xmlns="http://somenamespace"`), rather than `xmlns:rob="somenamespace"`, it specifies the default namespace for the document

```xml
<personxml:person 
     xmlns:personxml="http://example.com/same/url"
     xmlns:mycityxml="http://example.com/same/url" />
```
Here it wouldn't matter if you prefixed a given element with *personxml* or *mycityxml*, they'd both be treated as the same thing by an XML parser. The point is that an XML parser doesn't care what you've chosen as the prefix, only the namespace it maps too. The prefix is just an indirection pointing to the namespace.

```xml
<personxml:person xmlns:personxml="http://www.your.example.com/xml/person"
                  xmlns:cityxml="http://www.my.example.com/xml/cities">
    <personxml:name>Rob</personxml:name>
    <personxml:age>37</personxml:age>
    <cityxml:homecity>
        <cityxml:name>London</cityxml:name>
        <cityxml:lat>123.000</cityxml:lat>
        <cityxml:long>0.00</cityxml:long>
    </cityxml:homecity>
</personxml:person>
```

* all tags that start with `personxml:` belong to one XML
* all the ones that start with `cityxml:` belong to another XML

