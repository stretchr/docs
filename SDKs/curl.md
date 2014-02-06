# Using the `curl` command

`curl` is a tool to transfer data from or to a server, and can be used to easily interact with Stretchr's RESTful API.

All you need to know is:

  * Your account name
  * The project you want to interact with
  * Your API key

### Reading resources

To read a single resource:

```
curl -X GET https://account.stretchr.com/api/v1.1/project/resources/resource-id?key=API_KEY
```

To read many resources:

```
curl -X GET https://account.stretchr.com/api/v1.1/project/resources?key=API_KEY
```

### Deleting resources

To delete a single resource:

```
curl -X DELETE https://account.stretchr.com/api/v1.1/project/resources/resource-id?key=API_KEY
```

To delete many resources:

```
curl -X DELETE https://account.stretchr.com/api/v1.1/project/resources?key=API_KEY
```

### Creating resources

To create a resource using JSON:

```
curl -X POST --data "{\"name\":\"Laurie\"}" https://account.stretchr.com/api/v1.1/project/resources?key=API_KEY
```

To create many resources using JSON:

```
curl -X POST --data "[{\"name\":\"Laurie\"},{\"name\":\"Mat\"}]" https://account.stretchr.com/api/v1.1/project/resources?key=API_KEY
```

### Updating resources

To update a resource using JSON:

```
curl -X PATCH --data "{\"name\":\"Different\"}" https://account.stretchr.com/api/v1.1/project/resources/resource-id?key=API_KEY
```

### Replacing resources

To replace a resource using JSON:

```
curl -X PUT --data "{\"name\":\"Mat\"}" https://account.stretchr.com/api/v1.1/project/resources/resource-id?key=API_KEY
```