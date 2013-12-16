# Modifiers
There are a few features implemented in stretchr specifically for special situations - we call such features modifiers.

Modifiers are special parameters that modify the request in some way.  This section describes the modifiers and their impact.

<table>
	<tr><th> Modifier </th><th> Description </th><th> Example </th></tr>
	<tr><td>`method` </td><td> Tells Stretchr to treat the request as if it were made with the specified HTTP Method.  This is useful in JSONP situations where all requests must be a GET, but you want to remain RESTful. </td><td> `GET /people?method=POST` </td></tr>
	<tr><td> `always200=1` or `always200=true` </td><td> Tells Stretchr to always return a `200 OK` HTTP status code.  The actual status code will still be in the _Standard response object_.  This is useful for JSONp requests as some browsers ignore non-200 responses. </td><td> `GET /something-not-found?always200=1` </td></tr>
</table>

### No X-HTTP-Method-Override

Stretchr deliberately does not support the `X-HTTP-Method-Override` for security reasons, instead clients should use the `method` parameter described above.