# Including and Excluding
Data can be included and excluded from resources inside stretchr.

## Including Special Fields
All fields will be returned by default, with the exception of some optional system fields that provide special functionality.

<table>
<tr><th>Value</th><th>Description</th><th>Example</th></tr>
<tr><td>`~parent`</td><td>Including the parent will return the url for the parent resource.</td><td>`?include=~parent`</td></tr>
<tr><td>`~path`</td><td>Include the path for the current resource in the response</td><td>`?include=~path`</td></tr>
<tr><td>`~total`</td><td>Include the total in the response, useful when requesting a collection</td><td>`?total=true</td></tr>
</table>

## Excluding Data
You can exclude any field that you want from the response by just adding `?exclude={{field}}` to the url.  This can include system values or values inside the actual resource.

For example, if you want to request just the total number of resources that would be returned for a collection, you could request `?total=true&exclude=~items`.  This is a great way to minimize the load of the request and make it perform faster.