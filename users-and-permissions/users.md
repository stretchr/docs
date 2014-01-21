# Users
When a request comes in to Stretchr, it is anonymous by default and only subject to the permissions rules you defined for their API key.  You have the option of authenticating against Stretchr and including an additional auth key in your request.  There are benefits to authenticating your users, such as:

 * The ability to define custom user-based rules for things like not allowing users to edit content they didn't create
 * if included, Stretchr will track users throughout the system, recording which objects they created/edited

## Authenticating Users
Stretchr currently implements the following authentication methods

 * Google
 * Github
 * Facebook
 * Custom login _coming soon_

To authenticate a user, redirect them to `https://account.stretchr.com/api/v1.1/project/~auth/auth-provider`.  We'll run them through the oauth process and return a session code that you can use to authenticate users in future requests.  Just include the auth token as `auth=TOKEN` on the end of your URL.

This whole process is handled for you typically by the SDKs, for example, in the JS SDK you can authenticate user by simply running:
```
stretchr.login("github")
```
And all future requests will be authenticated with the user's info.

