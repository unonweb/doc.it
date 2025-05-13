
# JSON

```json
JSON.parse() // erzeugt aus einem JSON-formatierten Text ein entsprechendes Javascript-Objekt.
JSON.stringify(value) // converts an object or value to a JSON string, optionally replacing values if a replacer function is specified or optionally including only the specified properties if a replacer array is specified.

let all = []
all = [...JSON.parse(ex)] // [ "dsfg", 45, 5675 ]
all.push(...JSON.parse(ex)) // [ "dsfg", 45, 5675 ]


const data = JSON.stringify(object)

// a json array of objects
[
  {
    "name": "MySQL",
    "type": "RDBMS"
  },
  {
    "name": "MongoDB",
    "type": "NoSQL"
  },
  {
    "name": "Neo4j",
    "type": "Graph DB"
  }
]
```
