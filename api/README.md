# API Standards

## Table of Contents

  1. [Key Requirements](#key-requirements)
  1. [Resources](#resources)
  1. [HTTP Verbs](#http-verbs)
  1. [Url Naming](#url-naming)
  1. [Response](#response)
  1. [Serialization Practices](#serialization-practices)
  1. [Pagination](#pagination)
  1. [Security and Optimization](#security-and-optimization)
  1. [Version Your API](#version-your-api)

## Key Requirements

- It should use **web standards** if it doesn't feel unnatural.
- It should be consistent, intuitive and simple.
- It should be developer friendly and explorable.

## Resources

The principles of REST consist in separating the API into logical resources. These aren't necessarily the representation of a single internal model.

### Naming the resources

All the resources should be nouns and **not** verbs (e.g. users, blogs, posts, comments, etc.).

#### Should the endpoint name be singular or plural?
The keep-it-simple rule applies here. Although your inner-grammatician will tell you it's wrong to describe a single instance of a resource using a plural, the pragmatic answer is to keep the URL format consistent and always use a **plural**.

### Relations

If a resource can only exist within another resource these are the RESTful principles you can use:

- `GET /posts/31/comments`        - Retrieves a list of comments for post with id 31.
- `POST /posts/31/comments`       - Creates a new comment on post with id 31.
- `GET /posts/31/comments/25`     - Retrieves the comment with id 25 on post with id 31.
- `PUT /posts/31/comments/25`     - Updates the comment with id 25 on post with id 31.
- `PATCH /posts/31/comments/25`   - Partially updates the comment with id 25 on post with id 31.
- `DELETE /posts/31/comments/25`  - Deletes the comment with id 25 on post with id 31.

If a resource can exist independently it's advised to just include the resource's identifier in the parent. Alternatively if the client needs multiple related resources at the same time they should be bundled together into the same response to reduce the number of requests.

### Actions

After the resources are defined you need to find out what kind of actions should be applied to them. The basic CRUD actions can be omitted by simply using the HTTP request methods mentioned above. If an action doesn't fit into the CRUD operations there are two main approaches you can choose from:

1. Treat it like a sub-resource and modify it with CRUD operations. E.g. `PUT /posts/31/like` to like the post and `DELETE /posts/31/like` to dislike the post.
1. There are cases when you can't map the action to a single resource. In this case naming the endpoint with the name of the action makes the most sense, e.g. `/search`, `/sign-in`, etc.

**You should never ever** leak any implementation details via your API.

## HTTP Verbs

The HTTP request methods `GET`, `POST`, `PUT`, `PATCH` and `DELETE` have specific meaning in manipulating the resources.

### GET

- Retrieves a resource or a list of resources.
- It should **never** update a resource.
- Shouldn't even update the access logs.
- Use query strings instead of request body.

To retrieve the list of posts filtered by the keyword "awesome":
```javascript
GET /posts?search=awesome
```

To retrieve the post with id 31:
```javascript
GET /posts/31
```

### POST

- Creates a resource.
- It should be used when the server has to generate a unique key for the created resource.
- Multiple POST requests to the same URL create duplicate records.

To create a post:
```javascript
POST /posts
```
Request payload:
```javascript
{
  "title": "Awesome title",
  "content": "even more awesomeness"
}
```

> The newly created resource (with a unique identifier) should be returned in the response body.

### PUT

- Creates/updates a resource.
- Multiple PUT requests to the same URL don't create/update duplicate records.
- It should be used when the client is aware of the url of the resource.

To update the post with id 31:
```javascript
PUT /posts/31
```
Request payload:
```javascript
{
  "title": "Awesome title",
  "content": "even more awesomeness"
}
```

> If creating/updating the resource doesn't trigger unpredicted changes in the underlying data there is no need to return the resource in the response body.

### PATCH

- Partially updates a resource.
- Updated attributes of the resource should be provided only.

To update the content of the post with id 31:
```javascript
PATCH /posts/31
```
Request payload:
```javascript
{
  "content": "less awesomeness"
}
```

> No response body should be returned.

### DELETE

- Deletes a resource.

To delete the post with id 31:
```javascript
DELETE /posts/31
```

> No response body should be returned.

## URL Naming

It is a good practice to have all resources and actions written in `lowercase-separated-by-hyphens`, e.g. `/sign-in`, `/reset-password`, etc.

## Response

### HTTP Status Codes

A three-digit code indicates the result of each HTTP request made to the server.

#### 2xx - Success

Indicates that the request was successfully received, understood and accepted by the API.

#### 4xx - Client errors

Intended for cases in which the client seems to have erred. The server **should** include an entity containing an explanation of the error situation, and whether it is a temporary or permanent condition. These status codes are applicable to almost any request method.

#### 5xx - Server errors

The server is aware that it has erred or is incapable of performing the request. The server **should** include an entity containing an explanation of the error situation, and whether it is a temporary or permanent condition. These response codes are applicable to any request method.

> Only a few of the many possible error and status codes are commonly seen on the Internet.

### Possible status codes by Verbs

#### GET
- `200 OK` - The request is successful and the resource is retrieved.
- `404 Not Found` - The requested resource does not exist (in the current context).

#### POST
- `200 OK` - The request didn't result in a creation or multiple resources had been created which are retrieved.
- `201 Created` - A new resource has been created which is retrieved.
- `204 No Content` - The request didn't result in a creation or multiple resources had been created.

#### PUT
- `200 OK` - An existing resource has been modified which is retrieved.
- `201 Created` - A new resource has been created which is retrieved.
- `204 No Content` - An existing resource has been modified.

#### PATCH
- `200 OK` - An existing resource has been modified which is retrieved.
- `204 No Content` - An existing resource has been modified.
- `404 Not Found` - The requested resource does not exist (in the current context).

#### DELETE
- `200 OK` - An existing resource has been deleted which is retrieved.
- `202 Accepted` - An existing resource has been marked for deletion.
- `204 No Content` - An existing resource has been deleted.

#### In all cases
- `400 Bad Request` - The request is malformed, such as if the body does not parse or it has validation errors.
- `401 Unauthorized` - When no or invalid authentication details are provided. Also useful to trigger an auth popup on web or take the user to the login screen on mobile.
- `403 Forbidden` - When authentication succeeded but authenticated user doesn't have access to the resource.
- `405 Method Not Allowed` - When an HTTP method is being requested that isn't allowed for the resource or the authenticated user.
- `500 Internal Server Error` The server encountered an unexpected condition, which prevented it from fulfilling the request. 
- `501 Not Implemented` - The server does not support the functionality required to fulfill the request. This is the appropriate response when the server does not recognize the request method and is not capable of supporting it for any resource.
- `503 Service Unavailable` - The server is currently unable to handle the request due to a temporary overloading or maintenance of the server. The implication is that this is a temporary condition which will be alleviated after some delay. If known, the length of the delay may be indicated in a Retry-After header. If no Retry-After is given, the client **should** handle the response as it would for a 500 response.

### Allow overriding HTTP method

Some proxies support only POST and GET methods. To support a RESTful API with these limitations, the API needs a way to override the HTTP method.
Use the custom HTTP Header X-HTTP-Method-Override to override the POST Method.

### Error handling

The API should provide a useful error message in a known consumable format and should always return sensible HTTP status codes. The representation of an error should be no different than the representation of any other resource, just with its own set of fields. The error resource should provide a few things to the developer and the client - a useful error message and a unique error code is a must.

The message must describe the reason of the error and might provide a solution to fix it. The unique error code/constant should be a string and should describe the actual error in 2-3 words. The constant should be written in uppercase and the words should be delimited by underscores, e.g. `EMAIL_NOT_VERIFIED`, `TOKEN_EXPIRED`, etc. These constants can be used as keys to implement internationalization and also can help the developers and clients to properly handle specific errors without forcing them to read through the entire specification.

JSON output representation for something like this would look like:

```javascript
{
  "code": "POST_ARCHIVED",
  "message": "The post you are trying to add the comment to was archived."
}
```

> In non-production environments the error resource might contain additional fields (e.g. stacktrace, timestamp, etc).

## Serialization Practices

To provide better readability all the resource fields should be written in `snake_case`.

### Identifier

Use the field `id`. When there is a reference to another resource then prefix it with the name of the relation, e.g. a post would have an author as `author_id`:

```javascript
{
  "id": 1,
  "author_id": 12
}
```

### Boolean

Never include `is_` or `has_` prefixes, instead try to use adjectives and verbs in past tense.

For example:
```javascript
{
  "completed": true,
  "locked": false,
  "visible": true
}
```

> In case of having multiple flag-like booleans in the response consider using **bitmasking** instead.

### Bitmask

Multiple boolean flags can be transformed into numerical flags which are combined into a single numeric value using bitmasking.

```javascript
{
  "permission": {
    "can_create_comment": true,
    "can_delete_comment": false,
    "can_update_comment": false,
    "can_vote_comment": true
  }
}
```

becomes:

```javascript
{
  "permission": {
    "comment": 9
  }
}
```

where:

```javascript
const CAN_CREATE_COMMENT = 0b0001; // 1
const CAN_DELETE_COMMENT = 0b0010; // 2
const CAN_UPDATE_COMMENT = 0b0100; // 4
const CAN_VOTE_COMMENT   = 0b1000; // 8

permission.comment = CAN_CREATE_COMMENT | CAN_VOTE_COMMENT; // 1 | 8 = 9
```

### Count

Use the the name of the resource which you have counted with the `_count` suffix. Try to avoid the plural form of the resource name because that should mark a list and not a count.

For example:
```javascript
{
  "comment_count": 121
}
```

### Date

Use the `_at` suffix whenever possible.
Dates should be represented by timestamps (milliseconds since 1970.01.01).

For example:
```javascript
{
  "created_at": 1470191016653, // Wed Aug 03 2016 05:23:36 GMT+0300 (EEST)
  "updated_at": 1470391016653  // Fri Aug 05 2016 12:56:56 GMT+0300 (EEST)
}
```

### List

Try to use the plural form instead of suffixing it with `_list`, e.g. ~~`category_list`~~ should become `categories`.

For example:
```javascript
{
  "id": 13,
  "title": "How to format JSON"
  "tags": [
    "json",
    "format",
    "programming",
    "howto"
  ]
}
```
> If a request results in a list return the list directly instead of wrapping it into an object with a single field.

### Reference to another resource

When referencing a resource from within another resource there are two approaches:

#### Id notation

Include only the id of the referenced resource as a field named after the relation of the two resources suffixed with `_id`.
Use it if the client doesn't need to display any information about the referenced resource at this stage or already has the information referenced by that id.

For example a blog post would look like this:
```javascript
{
  "id": 13,
  "title": "How to format JSON",
  "author_id": 5 // the author of this post is the User with the id 5
}
```

#### Resource notation

Include the minimal required representation of the referenced resource named after the relation of the two resources.
Use it if the client needs to display information about the referenced resource and it doesn't have the information yet.

For example a blog post would look like this:
```javascript
{
  "id": 13,
  "title": "How to format JSON",
  "author": {
    "id": 5,
    "name": "Johnny Pack"
  }
}
```

## Pagination

The pagination should work without parameters as well and it should return the first set of elements.

### Page based

Two query parameters can be provided:
- `page`: it's the one-based index of the page
- `count`: it's the number of elements requested per page

For example:
```javascript
GET /posts/23/comments
// or
GET /posts/23/comments?page=1&count=5
```

The response consists of two parts:
- `elements`: contains the list of requested resources
- `page`: contains the meta-data related to pagination
  - `index`: shows the current page index
  - `max`: shows the number of available pages

For example:
```javascript
{
  "elements": [
    // ...
  ],
  "page": {
    "index": 1,
    "max": 10
  }
}
```

> If the page requested is lower than 1 or it's not a valid number the first page is returned.

> If the page requested is higher than the # of available pages the last page is returned.

### Resource based

It is also known as **infinite scroll**.
Two query parameters can be provided:
- `since_id`: it's the id of the last resource received in the previous result set
- `count`: it's the number of elements requested per page

For example:
```javascript
GET /posts/23/comments
// or
GET /posts/23/comments?since_id=547&count=5
```

The response is a list containing the requested resources.

For example:
```javascript
[
  // ...
]
```

> If the `since_id` in not present the first result set is returned.

> If the `since_id` references an unexisting resource an empty result set is returned.

> The last result set is reached if the number of received resources is less than requested (via the `count` parameter).

The `since_id` is used (instead of `date`) because this way the sorting and paging algorithm behind the API remains hidden from the client.

## Security and Optimization

### SSL

Use SSL whenever possible. It provides encryption even through unencrypted networks and simplifies authentication efforts. Simple API tokens will work just well.

**Be careful!** Once you enabled SSL on your API don't allow unsecured connections (e.g. plain http connections).

> **Note:** Setup SSL on the load-balancer instead of the servers whenever possible.

### Compression

Up to 80% bandwidth saving can be achieved when using gzip compression (according to Twitter and Stack Exchange). Although this requires additional CPU time to (un)compress the results, the trade-off with network costs usually makes it very worthwhile.

Most frameworks/servers provide an easy way to setup gzip compression and most of the time you can also configure a minimum response size which the compression is enabled on. This way you don't overload the CPU with compressing tiny chunks of responses.

In order to receive a gzip-encoded response you must do two things:

1. Set the `Accept-Encoding` header to gzip
1. Modify your user agent to contain the string gzip.

Here is an example of properly formed HTTP headers for enabling gzip compression:
```HTTP
Accept-Encoding: gzip
User-Agent: my program (gzip)
```

> **Note:** Setup compression on the load-balancer instead of the servers whenever possible.

## Version Your API

Make the API version mandatory and do not release an unversioned API. Use a simple ordinal number and avoid dot notation such as 2.5, e.g. `/api/v1/posts`.
