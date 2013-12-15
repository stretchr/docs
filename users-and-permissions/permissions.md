# Permissions
Stretchr provides fine grained control over data access using a simple rules language.  Rules can be assigned on a per-key basis, meaning each application that you develop can operate under a different set of rules.  This allows you to leverage your entire dataset or a small slice of it based on the needs of the application.

## Keys
First and foremost, data is accessed in Stretchr using keys.  You can create keys via your management portal.  To use your key, simply add it to the end of your request url.  For example:
```
https://project.account.stretchr.com/api/v1.1/collection?key=asdf
```

## Permissions
