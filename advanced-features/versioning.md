# Versioning

Specifying `versioning=1` or `versioning=true` in update or replace requests will cause a complete history of changes to be stored in a `/versions` sub-resource.

For example, given the following resource:

```
GET /people/mat

{
  ~id: "mat",
  name: "Mat",
  ~created: 1364503382,
  ~updated: 1364503382
}
```

The following update will cause the existing resource to be created under `/people/mat/versions`.

```
PUT /people/mat?versioning=1

{
  name: "Mathew"
}
```

The entire history is available:

```
GET /people/mat/versions

[
  {
    ~id: "3498hud9hgkdfhg98uhidngdfgj98",
    name: "Mat",
    ~created: 1364503382,
    ~updated: 1364503230,
    ~version-of: "people/mat"
  },
  {
    ~id: "3498hud9hgkdfhg98uhidngdfgj99",
    name: "Mathew",
    ~created: 1364503382,
    ~updated: 1364503231,
    ~version-of: "people/mat"
  }
]
```