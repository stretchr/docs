# Filters
Due to the schemaless nested data structure of stretchr, it allows you to take advantage of some unique querying features.  For example, if you've nested your data as such:

```
/companies/stretchr/people/ryan
/bands/beatles/people/paul
/schools/cu-boulder/classes/physics/people/robert-paulson
```

Then you have the ability to perform a global people search just by performing a `GET` on

```
https://account.stretchr.com/api/v1.1/project/people
```

Or, if you wanted to just get a list of people who sang in the Beatles, you could perform a `GET` on

```
https://account.stretchr.com/api/v1.1/project/bands/beatles/people
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

| Type | Format | Description | Example |
|---|---|---|---|
| Equal | `:{field}={value}` | Matches if `{field}` matches `{value}` | `:name=Mat` |
| Not equal | `:{field}=!{value}` | Matches if `{field}` does not match `{value}` | `:name=!Mat` |>
| One of | `:{field}={value1},{value2},{value3}` | Matches if `{field}` matches either `{value1}`, `{value2}` or `{value3}` | `:name=Mat,Laurie,Tyler` |
| All of | `:{field}={value1}&:{field}={value2}` | Matches if `{field}` matches both `{value1}` and `{value2}`.  Useful for array searches| `:employees=Ryan&:employees=Tyler`</tr>
| Not one of | `:{field}=!{value1},{value2},{value3}` | Matches if `{field}` doesn't match `{value1}`, `{value2}` or `{value3}` | `:name=!Mat,Laurie,Tyler` |
| Between | `:{field}={low}..{high}` | Matches if `{field}` is between `{low}` and `{high}` | `:age=18..30` |
| Exists | `:{field}=*` | Matches if `{field}` is present in the resource | `:address=*` |
| Doesn't exist | `:{field}=!*` | Matches if `{field}` is NOT present in the resource | `:address=!*` |
| Greater than | `:{field}=>{value}` | Matches if `{field}` is greater than {value} | `:count=>5` |
| Less than | `:{field}=<{value}` | Matches if `{field}` is less than {value} | `:count=<10` |
| Greater than or equal to | `:{field}=>={value}` | Matches if `{field}` is greater than or equal to {value} | `:count=>=5` |
| Less than or equal to | `:{field}=<={value}` | Matches if `{field}` is less than or equal to {value} | `:count=<=10` |

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
