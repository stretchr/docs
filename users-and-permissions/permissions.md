# Permissions
Stretchr provides fine grained control over data access using a simple JSON rules language.  Rules can be assigned on a per-key basis, meaning each application that you develop can operate under a different set of rules.  This allows you to leverage your entire dataset or a small slice of it based on the needs of the application.

To setup permissions, log into your Stretchr portal, select the appropriate project and key and click "Edit Permissions".

## Keys
First and foremost, data is accessed in Stretchr using keys.  You can create keys via your management portal.  To use your key, simply add it to the end of your request url.  For example:
```
https://project.account.stretchr.com/api/v1.1/collection?key=asdf
```

## Example
Permissions in Stretchr are defined based on the path that a request takes.  Due to the nested datastructure of Stretchr, this gives you the ability to provide powerful rules while maintaining clear understanding of what you're releasing.

For example, if you're app has a concept of accounts who each own their own data, you could nest all of that users data under their account resource, and then restrict access to only the user who owns the parent account.

```
{
  "accounts/{{accountId}}/***" : {
    "all" : "$loggedin && [accounts/$accountId].~createdby.id == $their.id"
  },
  "***" : {
    "all" : false
  }
}
```
There's a lot of powerful things goign on in those rules.  We explain the features in detail below, but here's a quick breakdown:

 * `"accounts/{{acountId}}/***"` - You define rules based on the url path that is used to access the data.  This particular rule means that any url that starts with `accounts/any-id/` is subject to the defined rules. `{{accountId}}` defines a variable that will be passed into your rules as $accountId.
 * `"all"` - The following rules apply to all actions (create, read, update, delete)
 * `$loggedin` - Returns true if the user is logged in or false if they aren't
 * `[accounts/$accountId] - Pulls a record from the database to check values
 * `.~createdby.id` - This is the value we care about from the `[accounts/$accountId]` record.  Stretchr tracks who created/updated every object, allowing you to validate against it.
 * `$their.id` - `$their` gives you access to the current users record so that you can use it for validation
 * `***` - Stretchr gives you access to all levels of your tree, so if you have data stored at `accounts/1/books`, you could also access that data by heading to `/books`.  As a result, we always create a default catch-all rule that matches every path and blocks access to it.  The above rules will block all requests that don't start with `/accounts/{{accountId}}`

## Basic Syntax
At the most basic level, a permissions rule will consist of a path, an action and a true/false statement that decides if the request is authorized:

```
"path/to/record" : {
	"action" : true/false
}
```

### Paths & Path Variables

Paths represent the url path used to make the request.  You can use wildcards and param values in the path.  For example, `/accounts/{{accountId}}/***` will match a request of `/accounts/1/books` and will pass `1` into the rules engine as variable `$accountId`.

You can define options values using `[]`, for example: `/accounts/[id]` will match both `/accounts` and `/accounts/2`

### Path Wildcards
Rules support wildcards in path declarations.  You can do things like:
```
"/accounts/1/***" // Matches any path that starts with /accounts/1
"/***/comments" // matches any path that ends in /comments
"/***/comments/***" // matches any path that has /comments in it
```

### Nested Paths

Because Stretchr allows you to access data from multiple angles, it's important to remember that even if you block access to a books collection using `/accounts/{{accountId}}/books`, you can still access the `books` by hitting `/books`.  As a result, we recommend that all rules end with a default rule of:
```
{
  "***" : {
    "all" : false
  }
}
```
This will ensure that all requests are blocked by default - then you can go through and allow specific nested routes as needed.

### Actions
Rules can be limited to the action a specific request is attempting to perform.  These mostly map to your standard CRUD operations.

| Action | Description |
| --- | --- |
| `create` | create new resources |
| `read` | read resources |
| `update` | update existing resources |
| `delete` | delete resources |
| `modify` | create, update and delete resources |
| `all` | all actions |

### Accessing Data for Rules
You can use any data in your datastack for creating rules.  We also provide a few convenience variables that are common

| Variable | Description | Example |
| --- | --- | --- | 
| `$their` | Access data for the currently logged in user for the request.  You'll only have access to this var if the request included a `auth` key. | `$their.id` or `$their.email` |
| `$loggedin` | Tells you if a valid `auth` key was included in the request | `$loggedin` |
| `.field` | You have immediate access to the record being accessed in the request, so a request for `/books/1` will let you access `.title` or `.author` directly | `.~createdby.id == $their.id` |
| `[collection/id].field` | Loads the resource stored at `collection/id` and gives you access to all its data | `[accounts/$accountId].~createdby.id == $their.id` |

