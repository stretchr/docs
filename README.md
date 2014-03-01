# Stretchr Documentation

Stretchr is a schemaless datastack designed to let you stop worrying about your data and start deciding what to do with it.  It replaces the need for most backend and API development by providing a powerful query interface, RESTful API and users with fine-grained permissions.  

## Why Stretchr?
| Features | With Stretchr | Without Stretchr|
|---|---|---|
| Nested Relations | Stretchr provides nested relationships between data.  For example, a `todo` resource could have a comment located at `/todos/1/comments/1`.  That same comment is accessible at `/comments/1` | Traditionally, acheiving this kind of data flexibility in NoSQL is handled by duplicating data |
| Querying | Stretchr provides complex querying capabilities to client applications.  Learn more about specific querying options on the [querying page](/querying/filters.md). | Without Stretchr, you won't want to expose this level of flexibility to the world without dedicating a backend application to protect it|
| Users | Oauth solutions are built in, adding support for users is usually just a single line of code. | Oauth implementations require custom backend development and must be handled explicitely for each oauth provider. |
| Permissions | Permissions in Stretchr can be customized per app, user or down to an individual data resource.  The flexibility lets you define just about any level of access you need. | Without Stretchr, you'll be implementing your own permissions system in your backend layer.  As the needs of your application evolve, these permissions will need to be updated to support new functionality. |
| Integration | Stretchr provides a variety of [open source SDKs](https://github.com/stretchr) that let you get started pushing and pulling data immediately.  No need to create databases or collections. | Without Stretchr, you'll be looking at a full installation of a NoSQL solution, and then finding an adapter to work with.  You definitely won't find any front end JS or Cocoa SDKs, we promise :) | 

## Getting Started
The simplest way to get started is to try out our multitenant cloud environment.  To sign up, head on over to [stretchr.com](http://www.stretchr.com).  If you're interested in learning how Stretchr can be deployed inside your organization, get in touch on our [enterprise page](http://www.stretchr.com/enterprise).