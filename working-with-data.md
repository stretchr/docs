# Working with Data

## Resources
Resources in Stretchr are schemaless data objects that can look like anything:

```json
{
  "~id": "1",
  "name": "Stretchr",
  "version": 1,
  "launched": "198641896151",
  "live": true,
  "domains": ["stretchr.com", "stretchr.org"],
  "info": {
    "description": "RESTful Datastore",
    "url": "http://www.stretchr.com/"
  }
}
```

### Collections of Resources
Resources are always divided into unique collections that can span across various levels of nesting.  For example, the following resources all belong to the same `people` collection:
```
/people/1
/companies/stretchr/people/2
/bands/beatles/people/3
```
As a result, they can all be accessed with a simple query to:
```
/people
```

The ability to nest data to match real world relationships, while also maintaining access across each level of the nesting structure seperately is one of the more powerful features of Stretchr.  The benefits may not seem obvious at first, but over time the ability to query a collection of resources across all domains or within a specific one yields tons of interesting possibilities.

## RESTful API

### Basic URL structure
Accessing your data inside Stretchr is done using a predictable RESTful API format.

```
https://{account}.stretchr.com/api/{v1.1}/{project}/{parent-collection}/{parent-id}/{collection}/{child-id}
```
That can be broken down as follows:

<table>
	<tr><th>Field</th><th>Description</th><th>Example</th></tr></tr>
	<tr><td>account </td><td> The name of your account will be the same across all your projects </td><td> birdsinc</td></tr>
	<tr><td>v1.1 </td><td> The version of the API you are writing against </td><td> v1.1</td></tr>
  <tr><td>project </td><td> The name of your project </td><td> birdwatcher</td></tr>
	<tr><td>parent-collection </td><td> The parent collection you're pulling from </td><td> birds</td></tr>
	<tr><td>parent-id </td><td> A URL friendly ID for the parent resource </td><td> cardinal</td></tr>
	<tr><td>collection </td><td> The collection that you're pulling a resource from </td><td> sightings</td></tr>
	<tr><td>child-id </td><td> A URL friendly ID for the resouce </td><td>123</td></tr>
</table>

Using the examples along the right, you'd be requesting the resource `123` inside the `sightings` collection nested under `birds/cardinals`.  To put that into a sentence, think "Tell me about the cardinal sighting with ID 123".  If you want to view all sightings, you could drop the `123` ID and think "Give me all the sightings of the cardinal".

### Nesting Data
Stretchr follows a strict `collection/ID` pair format which makes it very easy to see which resource is being referred to just by looking at the URL.

For example the following path refers to the third chapter of a book with ID 'origin-of- species' written by an author with ID 'darwin'.
```
/authors/darwin/books/origin-of-species/chapters/3
```
Notice that the URL follows the format collection, id, collection, id, collection, id.  If you wanted to view all the books written by darwin, you could hit
```
/authors/darwin/books
```
Or, to view all the chatpers of `origin-of-species`, you could hit
```
/authors/darwin/books/origin-of-species/chapters
```

Here's where things get interesting.  Let's say you wanted to view all books across all authors?  Easy, just hit
```
/books
```

### HTTP Methods
Because Stretchr is RESTful, you interact with your data using the language of the web.  That means that all your basic CRUD methods apply.

| Method | URL Format | Description |
|---|---|---|
| GET | /collection | Get all the resources inside the collection |
| GET | /collection/id | Get a single resource |
| PUT | /collection/id | Replace or create a resource with a new one.  If no resource exists at that ID, it will be created |
| PUT | /collection | Replace or create a resource with a new one, the `~id` should be included in the body in this case |
| POST | /collection | Create a new resource in the collection.  If no `~id` is specified, it will be generated for you |
| PATCH | /collection/id | Update data with the existing resource.  Any included data will replace existing data, but data not included will be left alone |
| DELETE | /collection/id | Deletes a resource from the collection |
| DELETE | /collection | Deletes the ENTIRE collection |


## Responses
Stretchr responds to all requests with a standard response object, making it easy to parse out the data you need while also providing context.  Here is the standard response

```json
{
  "~status": 201,
  "~data": {"name":"Stretchr"},
  "~context": "context",
  "~errors": [{"message":"This is how an error looks"}],
  "~changes": {"~changed": 1, "~updated": 1, "~deltas": 0, "~ids": ["1", "2", "3"]}
}
```
`~status` is the only guaranteed item in every response.

### Collection Response
In addition to the standard response above, responses that contain many resources will also contain a standard object inside of `~data`
```json
"~data": {
  "~count" : 1000,
  "~total" : 2000,
  "~items" : [/* collection goes here */]
}
```
This will give you an idea of how many objects you were given, how many total objects matched your original request as well as the objects themselves.  This information is very useful for paging.

### Changes
Because Stretchr implements timestamps, user tracking, and other things for you, it will sometimes return a change object in the event of creating/updating/deleting resources.  The change object will include the following information:

 * `~changed` shows how many resources were impacted
 * `~updated` shows how many resources were updated
 * `~deltas` returns an array of data that changed on the server.  For example, it will include the created/updated time for the resource.
 * `~ids` shows the array of resources that were changed, this array will line up with the deltas array above