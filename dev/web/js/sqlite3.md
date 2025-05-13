methods
-------------

...a handful of different methods for executing queries:

* `run`: used to create or alter tables and to insert or update table data
* `get`: select a single row of data from one or more tables
* `all`: select multiple rows of data from one or more tables

### run

```js
db.run('SOME SQL QUERY', [param1, param2], (err) => {
  if (err) {
    console.log('ERROR!', err)
  }
})
```

* first parameter: a string of SQL to be executed (the only required parameter)
* second: an optional array of parameters that the sqlite3 library will swap in for any '?' placeholders within the query
* third: an error callback function.