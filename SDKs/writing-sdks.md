## Writing SDKs

Software development kits (SDKs) are a great way to let customers interact with Stretchr's services, because they allow the end programmer to speak their own language.  If you intend to use Stretchr, and there is no existing SDK, please consider starting an SDK and contributing to the open source Stretchr community.

### Who is this document for?

This document is for people who will be using Stretchr's services in a language where there isn't already an SDK.  Be sure to check out our [list of SDKs on GitHub](https://github.com/search?q=%40stretchr+sdk) before embarking on writing your own.  And PLEASE consider getting in touch with us if you want to make your SDK official by having it included in our repos and on our website.

### Goals

SDKs have the following design goals:

  * Simple and easy to use
  * Fits in with the style and syntax of the language it is targeting
  * Able for your users to easily write unit tests with your SDK
  * Test-driven development: unit tests prove that your code works

### Concepts

Simply put, a Client uses a Transport to make Requests and return Responses, and you can get Resources or ResourceCollections from the Response, or you can make a Resource from a Client and save it to Stretchr.

  * `Client` is your apps way of talking to Stretchr
  * `Transport` is the Client's way of talking to the remote servers
  * `Request` is an action to take, create, read, update or delete
  * `Response` is the response to an action taken by a `Request`
  * `Resource` and `ResourceCollection` are objects for representing the actual data stored in Stretchr
  * `Error` holds the message of an error that has occurred on the server

Resources are a higher level concept that represent individual resources in Stretchr.  A resource is a single document.  And collections of resources are often explicltly treated in SDKs.

A Resource knows about the `Client` too, which allows them to save, update or delete themselves.

### Objects

#### `Client` object

Every request into Stretchr shares some common information, such as project name, API key, and other configuration and we call this a `Client`.  Users will likely create one of these objects per app and store it as a global singleton, or else pass it around to services internally that need it.

A client has the following dities:

  * Able to build complete URLs for requests
  * Able to actually make requests through a `Transport` (important for testing purposes)

A client looks like this:

```
Client {
  apiKey string       // the API key: "abc123"
  host string         // the URL host of the services: "project.company.stretchr.com"
  apiVersion string   // the API version: "1.1"
  protocol string     // the protocol through which to access services: "https"
  transport Transport // the Transport object to use to make real requests
}
```

It makes sense for the client to be responsible for interacting with the services, such as making web requests etc.  This makes writing unit tests easy, because users only have to stub out the actual communication in one place.

##### Getting the base URL

Since the `Client` knows the `host`, `apiVersion` and `protocol`, it is able to generate the base url by simply concatinating the string like this:

    protocol + "://" + host + "/api/v" + apiVersion + "/"

The `Request` object will use this when generating the full URLs.

##### Making request objects from a client

The `Client` should allow you to make `Request` objects by also specifying a path and the `Request` should keep a reference to the `Client` that created it, since this is how it will make requests.

##### Making requests

To actually make a request, the client would ask the `Transporter` to do the work, but it's nice to expose from the `Client` so that users don't need to know about transporters at all.

##### Making resources

The `Client` should also be able to create new resources, for when they are not being loaded from the server.  A `Resource` needs a path, and may or may not have some data.

The newly created `Resource` object should have:

  * a path set (maybe the signature is `client.new(path)`)
  * the appropriate client set

Users will then add data to the resource, and call one of the action methods to create it.

#### `Transport` object

A `Transport` object belongs to a `Client` and knows how to make real requests.  When you are are writing unit tests for your SDK, you can replace this object with a `TestTransporter` and make assertions on the behaviours.  Users of your service can also do this, allowing you and them to run tests without hitting remote servers.

```
Transport {
  Response makeRequest(Request) // makes a Request and returns a Response
}
```

The `Transport` only has to make requests, and return responses, so the interface is pretty simple.

#### `Request` object

A `Request` object describes a single request.  A request can either create, read, update or delete data.  A request should be constructed with a `Client` and a path at least.

```
Request {
  client Client      // the Client that this request will use
  path string        // the path of the request: "people/mat/books"
  params dictionary  // a map of parameters
  filters dictionary // a map of filters
  data object        // (optional) object containing data to send in request
  method string      // the HTTP method the request will use
}
```

The Request is responsible for building the entire URL, by adding the base URL from the `Client`, the path, params and filters.

    client.baseURL + path + "?" + parameters + "&" + filters

The `data` object in the request is what will be encoded and sent to Stretchr in the body, this is used when creating or updating (or replacing) resources.

##### Helper methods for parameters

Along with a method to allow you to directly set parameters for the request, such as `setParam(key, value)` you might also consider adding helper methods to your `Request` object for setting common parameters.  For example, `order`, `limit` and `skip` are common parameters, so you could expose them as their own methods like the Stretchr JavaScript and HTML5 SDK does in a fluent way:

    stretchr.at("people/123").order("age").skip(10).limit(5)

Notice also the `at` method, that's the method on the `Client` object that creates a new `Request`.

##### Helper methods for taking action

Once you have built a `Request` you will need to let your users perform the action that the request describes.  Such methods include:

  * read()
  * update()
  * create()
  * delete()
  * replace()

NOTE: Most of the time, users will only use the `read` method, and will instead call methods on the resource itself which will in turn call these methods.

Since these methods cause some kind of communication (likely a HTTP web request), you will need to think about the best way for users to receive the response.

In JavaScript, passing in a callback function is a nice solution.  In Cocoa, perhaps passing in a block works best, or even blocking the thread until it's completed.  This is your area, as you're the designer of the SDK and you know what best suits your language and environment.

The Stretchr JavaScript and HTML5 SDK uses an events model to allow users to capture responses, they can either bind to the `after` event (which always occurs after a request has been made), or `success` and `error` events.

For example, consider the following JavaScript code:

```
stretchr.at("people").read({

  error: function(response) {
    alert("Something went wrong - please try again later.");
  },

  success: function(response) {
    var resources = response.resources();
    // do something with the people
  }

});
```

#### `Response` object

The `Response` from making a `Request` to Stretchr should be encapsulated into its own object, to make it easy to understand the impact of a request.

```
class Response {
  data object   // the data object returned
  errors array  // an array of errors
  status int    // the HTTP status code of the response
}
```

WATCH OUT: Stretchr responses do not contain unnecessary information, so if there were no errors there will not be an `~errors` key in the returned data.

##### Sucessful or not?

To decide if a request was successful or not, you should add a `success()` method that performs the following test:

    response.status >= 100 && response.status < 400

Essentially, this is testing that an acceptable HTTP Status Code was returned.

##### Handling errors

If the response is not successful, there will be an `~errors` array in the response (see the `Error` object).  It's an array because of the concurrent nature of Stretchr, it is possible for more than one error to occur.

##### Using the data

Once users have established that they have a successful response, they will want to get the data out of the object and make use of it.  While you can just deliver the object directly, we recommend providing methods for extracting specific types.

The `~data` key can contain either a single data object, or a collection type containing many resources.

  * A `getResource()` method should be able to extract a single `Resource` from the data.
  * A `getResources()` or `getResourceCollection()` method should extract the details from a collection object (see `ResourceCollection`) which in turn will contain an array of `Resource` objects.

#### `Error` object

An `Error` object describes a single error that could occur in Stretchr.

```
Error {
  Message string // the error message
}
```

#### `Resource` object

A Resource represents a single document stored in Stretchr.

```
Resource {
  client Client
  data object
  path string
}
```

  * Storing a reference to the `Client` inside a `Resource` is useful for when you want to call actions directly on the resource itself.  The resource will be capable of making its own requests.
  * The `data` object will contain all the data of the resource.
  * The `path` describes the URL path where the resource lives, or where it will live.  This should be set by the `Request` when reading resources and should NOT include the ID.

##### Making new resources

You need to allow your users to create new `Resource` objects, while specifying at least the client and path.  In the Stretchr JavaScript and HTML5 SDK, this was done like this:

    var resource = stretchr.at("people/1/books").new()

Technically this is overkill, because the `at` method creates a `Request` object which is then discarded.  But it's worth it for the syntactical sugar.  Depending on the language, it might make more sense for you to just give them a method on the `Client` object, like the Cocoa SDK:

    STResource *resource = [client newResourceWithPath:@"people/1/books"];

##### Resource ID

Every resource will have a unique ID stored with the `~id` key, you should expose this explicitly through methods allowing users to easily get the value.

    function id() { return this.data["~id"]; }

You can have a setID method too if you like, but this tends not to happen very often in normal usage.

##### Update(), Reload(), Remove(), Replace() methods

Once a user has loaded a `Resource`, they might want to make changes to it and push those changes to the server.  To enable this, you should provide methods that generate a request using the client, and use the action methods on the `Request` object.

These methods should:

  * ask the `Client` for a new `Request` with the `path` of this resource
  * set the appropriate HTTP method on the `Request`
  * if not a `GET`, set the `body` of the `Request`
  * ask the `Client` to make the request (which will in turn as the `Transporter` to do so)

However you decide to handle completion code (callbacks, blocks etc.) your action methods should also pass this through to the client.

##### Capturing and using the change info

Repsonses to change actions will contain a change info object.  The change info contains details about what has changed on the server for each object.  For example, if you create an object without specifying an ID, the server will generate an ID and return it in the change info.  Your `Resource` object should merge the response of the change info into its own data to ensure the data is in sync with the server.

##### Save() helper method

If your `Resource` objects track whether they are dirty or not (i.e. has anything changed since we loaded it from the server?) you can provide a `save()` method for your users, which essentially has the following functionality:

  * Is there an ID in the data?  (i.e. has the data been saved to the server)
    * No: Create the `Resource`
    * Yes: Is the `Resource` dirty, i.e. has anything changed?
      * No: Do nothing (just call the success right away)
      * Yes: Update the `Resource`

#### `ResourceCollection`

The `ResourceCollection` object represents a collection of `Resource` objects, as well as some metadata about the collection itself, such as the number of items, the total items etc.

```
ResourceCollection {
  Items int   // the number of items in this collection (i.e. len(Items))
  Total int   // the total number of items in the entire collection
  Items array // the
}
```