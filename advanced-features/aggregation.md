# Aggregation

Stretchr provides a simple, dynamic aggregation API for use against your collections.  To build up an aggregation, just append an `agg=function().function()` param to your api call.

For example: `/people?agg=group(state,city).avg(age)` will group the data in your `people` collection by state and city and then give you the average age.

<table>
	<tr><th> Function </th><th> Description </th></tr>
	<tr><td>`group(key1,...keyN)` </td><td> Group the result by the given keys. </td></tr>
	<tr><td>`sum(key1,...keyN)`</td><td>Calculate the sum of the given keys.</td></tr>
	<tr><td>`max(key1,...keyN)`</td><td>Find the maximum of the given keys.</td></tr>
	<tr><td>`min(key1,...keyN)`</td><td>Find the minimum of the given keys.</td></tr>
	<tr><td>`avg(key1,...keyN)`</td><td>Calculate the average of the given keys.</td></tr>
	<tr><td>`uniqueSet(key1,...keyN)`</td><td>Return an array of all unique values at the given keys.</td></tr>
	<tr><td>`unwind(key1,...keyN)`</td><td>If the value is an array, explode it into copies of the whole object. Allows aggregation against an array of objects.</td></tr>
	<tr><td>`count()`</td><td>Return the number of occurances for each result in the query.</td></tr>
</table>

When using any of the calculation functions (`sum`,`min`,`max`,`avg`), the result key returned will be `<function>-<key>`. For example, `max(count)` will return `"max-count":162`.

For an example of what the aggregation API can do, take a look at [San Diego Crime - Most Dangerous Communities][1] and [San Diego Crime - Most Dangerous Communities with Crime Type][2]

The above links demonstrate the power of the aggregation framework. Simply typing your functions in a fluent manner allows you to construct queries against your data, gaining new insights instantly. Try typing out your own queries. I wonder what the most dangerous time of day is?

[1]: https://sandiego.stretchr.com/api/v1.1/crime/incidents.json?key=de0128c1cb7b70f583f56dd71da857df&agg=group(community).count()&order=-count
[2]: https://sandiego.stretchr.com/api/v1.1/crime/incidents.json?key=de0128c1cb7b70f583f56dd71da857df&agg=group(community).uniqueSet(type).count()&order=-count
