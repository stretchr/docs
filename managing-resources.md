# Managing resources

Stretchr is a RESTful service, which means you deal in resources in a predictable way.  If you are unfamiliar with RESTful patterns, this seciton gives you a quick overview.

## Reading

### Reading one resource

To read a resource, just perform a `GET` on the path of the resource.  For example, if you have a `books` resource with an ID of `origin-of-species`, you could read it by:

```
GET /books/origin-of-species
```

### Reading many resources

To read many resources, just perform a `GET` on the path of the resources you wish to read.  For example, to read all books, you would do:

```
GET /books
``` 

  * See _Querying_ below for details on how to fine tune your results.
 
### Creating a resource

To create a resource in Stretchr, just `POST` a document to the relevant path.  For example, to create a new book you could do this:

```
POST /books

{
  "~id": "the-lean-startup"
  "author": "Eric Ries",
  "title": "The Lean Startup"
}
```

Notice in the example above we specify a `"~id"` field, Stretchr allows you to do this if you want to specify the ID.  Omitting the field will cause Stretchr to generate a random ID for you.

The response from the server will contain a change object describing 

#### Timestamps

By default Stretchr creates and manages _created_ and _updated_ timestamps (in Unix time).  You can prevent this behaviour by specifying `~exclude=~stamps` in the request parameters.

For example, to create the same book as above without timestamps, you would do:

```
POST /books?exclude=~stamps

{
  "~id": "the-lean-startup"
  "author": "Eric Ries",
  "title": "The Lean Startup"
}
```

### Creating many resources in one request

It is possible to create many resources in one request.  You do this by `POST`ing a collection (array in JSON) of resources to the relevant path.

For exmaple, to create three books in one request, you would do:

```
POST /books

[
  {"title": "Book one"},
  {"title": "Book two"},
  {"title": "Book three"}
]
```

The `~deltas` array in the change information will return with three objects, in the correct order, describing the IDs and timestamps for these new resources.  For more information see _Deltas_.

### Updating a resource

To make changes to an existing resource, you can simply `PUT` the changes to the resources path.

For example, to update a resource at `/books/123`, you would do this:

```
PUT /books/123

{
  "title": "The new title"
}
```

The title of the book with ID `123` would be changed.

### Replacing a resource

Stretchr makes a distinction between updating and replacing resources.  An _update_ action will preserve all the data not specified in the posted resource, whereas a _replace_ action will preserve only the ID.

For example, given this resource:

```
GET /books/123

{
  "title": "My Book",
  "subtitle": "How I came to write something"
}
```

A replace action (which is a `POST` to the book's path) like this:

```
POST /books/123

{
  "title": "My Great Book With No Subtitle"
}
```

would actually turn the resource into:

```
GET /books/123

{
  "title": "My Great Book With No Subtitle"
}
```

Notice how the `subtitle` data has been lost.

### Deleting resources

To delete a resource or multiple resources, you perform a `DELETE` action on the relevant path.

To delete a single book you would do:

```
DELETE /books/123
```

To delete all books you would do:

```
DELETE /books
```

When deleting multiple resources, you can use some of the features described in _Querying_ below, but you should read the _Filtering with DELETE_ section for details on current limitations.