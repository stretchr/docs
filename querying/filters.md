# Filters
Due to the schemaless nested data structure of stretchr, it allows you to take advantage of some unique querying features.  For example, if you've nested your data as such:
```
/companies/stretchr/people/ryan
/bands/beatles/people/paul
/schools/cu-boulder/classes/physics/people/robert-paulson
```
Then you have the ability to perform a global people search just by performing a `GET` on
```
https://people.random.stretchr.com/api/v1.1/people
```
Or, if you wanted to just get a list of people who sang in the Beatles, you could perform a `GET` on
```
https://people.random.stretchr.com/api/v1.1/bands/beatles/people
```
## Parameter Filtering
In addition to nested data filtering, you can also filter on specific data attributes within resources.  A filter parameter is any query string added to your URL that starts with `:`

For example, to find all women from a collection of people you could do
```
/people?:gender=female
```
The general format to use for filters is
```
:{fieldname}={match}
```
<table>
<tr><th>Type </th><th> Format </th><th> Description </th><th> Example </th></tr>
<tr><td> Equal </td><td> `:{field}={value}` </td><td> Matches if `{field}` matches `{value}` </td><td> `:name=Mat` </td></tr>
<tr><td> Not equal </td><td> `:{field}=!{value}` </td><td> Matches if `{field}` does not match `{value}` </td><td> `:name=!Mat` </td></tr>>
<tr><td> One of </td><td> `:{field}={value1},{value2},{value3}` </td><td> Matches if `{field}` matches either `{value1}`, `{value2}` or `{value3}` </td><td> `:name=Mat,Laurie,Tyler` </td></tr>
<tr><td> All of </td><td> `:{field}={value1}&:{field}={value2}` </td><td> Matches if `{field}` matches both `{value1}` and `{value2}`.  Useful for array searches</td><td> `:employees=Ryan&:employees=Tyler`</tr>
<tr><td> Not one of </td><td> `:{field}=!{value1},{value2},{value3}` </td><td> Matches if `{field}` doesn't match `{value1}`, `{value2}` or `{value3}` </td><td> `:name=!Mat,Laurie,Tyler` </td></tr>
<tr><td> Between </td><td> `:{field}={low}..{high}` </td><td> Matches if `{field}` is between `{low}` and `{high}` </td><td> `:age=18..30` </td></tr>
<tr><td> Exists </td><td> `:{field}=*` </td><td> Matches if `{field}` is present in the resource </td><td> `:address=*` </td></tr>
<tr><td> Doesn't exist </td><td> `:{field}=!*` </td><td> Matches if `{field}` is NOT present in the resource </td><td> `:address=!*` </td></tr>
<tr><td> Greater than </td><td> `:{field}=>{value}` </td><td> Matches if `{field}` is greater than {value} </td><td> `:count=>5` </td></tr>
<tr><td> Less than </td><td> `:{field}=<{value}` </td><td> Matches if `{field}` is less than {value} </td><td> `:count=<10` </td></tr>
<tr><td> Greater than or equal to </td><td> `:{field}=>={value}` </td><td> Matches if `{field}` is greater than or equal to {value} </td><td> `:count=>=5` </td></tr>
<tr><td> Less than or equal to </td><td> `:{field}=<={value}` </td><td> Matches if `{field}` is less than or equal to {value} </td><td> `:count=<=10` </td></tr>
</table>
## Filtering within nested data
Let's suppose you have a resource located at `/people/ryan` that looks like this:
```json
{
  "name": {
    "first":"ryan",
    "last":"quinn"
  }
  "company":"stretchr",
}
```

You could then filter for the result with
```
/people?:name.first=ryan
```

## Filtering within arrays
Stretchr also allows you to search complicated structures inside arrays.  For example, if a resource located at `/companies/stretchr` looks like this
```json
{
  "name" : "stretchr",
  "employees" : [
  	{
  	  "name" : {
  	    "first":"ryan",
        "last":"quinn"
      }
  	},
    {
      "name" : {
        "first" : "mat",
        "last" : "ryer"
      }
    },
    {
      "name" : {
        "first" : "tyler",
        "last" : "bunnel"
      }
    }
  ]
}
```
You could search for that result by running
```
/companies?employees.name.first=ryan,tyler
```

That would find all companies that have an employee with a first name of ryan or tyler.
