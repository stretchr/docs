## Ordering and Sorting

Stretchr makes it easy to order the resources by using the `~order` query parameter.

* Passing `order=age` would order by age ascending
* Passing `order=-age` would order by age descending

Passing a comma separated list to the `~order` parameter will cause it to perform multiple ordering, for example, to sort first by `age` (with oldest first) and then by `class`, you would do:

```
GET /people?~order=-age,class
```

# Paging
By default, Stretchr returns a limited number of resources in each response (and this number is subject to change), so developers should be aware of the limitations.

Controlling your own page size is usually the best approach anyway, which you can do via the `limit` and `skip` parameters.

* `limit` tells Stretchr how many resources to return and,
* `skip` tells Stretchr how many resources to skip before it starts returning (or the index (+1) of the first resource to get)

For example, to get the second page of a collection of resources with 10 resources in each page, you would do:
```
GET /people?limit=10&skip=10
``` 
* The skip value can be calculated using `currentPage * limit`
