## Ordering and Sorting
Stretchr makes it easy to order the resources by using the `~order` query parameter.

* Passing `~order=age` would order by age ascending
* Passing `~order=-age` would order by age descending

Passing a comma separated list to the `~order` parameter will cause it to perform multiple ordering, for example, to sort first by `age` (with oldest first) and then by `class`, you would do:

```
GET /people?~order=-age,class
```
