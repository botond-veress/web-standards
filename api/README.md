## Key Requirements

- It should use **web standards** if it doesn't feel unnatural;
- It should be consistent, intuitive and simple;
- It should be developer friendly and explorable;

## Resources

The principles of REST consist in separating the API into logical resources. The HTTP request methods `GET`, `POST`, `PUT`, `PATCH` and `DELETE` have specific meaning in manipulating these.

All the resources should be nouns and **not** verbs (e.g. users, blogs, posts, comments, etc.). These aren't necessarily the representation of a single internal model.

- `GET /posts`        - Retrieves a list of posts
- `POST /posts`       - Creates a new post
- `GET /posts/31`     - Retrieves the post with id 31
- `PUT /posts/31`     - Updates the post with id 31
- `PATCH /posts/31`   - Partially updates the post with id 31
- `DELETE /posts/31`  - Deletes the post with id 31

### Relations

If a resource can only exist within another resource these are the RESTful principles you can use:

- `GET /posts/31/comments`        - Retrieves a list of comments for post with id 31
- `POST /posts/31/comments`       - Creates a new comment on post with id 31
- `GET /posts/31/comments/25`     - Retrieves the comment with id 25 on post with id 31
- `PUT /posts/31/comments/25`     - Updates the comment with id 25 on post with id 31
- `PATCH /posts/31/comments/25`   - Partially updates the comment with id 25 on post with id 31
- `DELETE /posts/31/comments/25`  - Deletes the comment with id 25 on post with id 31

If a resource can exist independently it's advised to just include the resource's identifier in the parent. Alternatively if the client needs multiple related resources at the same time they should be bundled together into the same response to reduce the number of requests.

#### Should the endpoint name be singular or plural?
The keep-it-simple rule applies here. Although your inner-grammatician will tell you it's wrong to describe a single instance of a resource using a plural, the pragmatic answer is to keep the URL format consistent and always use a **plural**.

## Actions

After the resources are defined you need to find out what kind of actions should be applied to them. The basic CRUD actions can be omitted by simply using the HTTP request methods mentioned above. If an action doesn't fit in the CRUD operations there are two main approaches you can choose from:

1. Treat it like a sub-resource and modify it with CRUD operations. E.g. `PUT /posts/31/like` to like a post and `DELETE /posts/31/like` to dislike the post.
1. There are cases when you can't map the action to a single resource. In this case naming the endpoint with the name of the action makes the most sense e.g. `/search`, `/sign-in`, etc.

**You should never ever** leak any implementation details via your API.

## Security and optimization

### SSL

Use SSL whenever possible. It provides encryption even through unencrypted networks and simplies authentication efforts. Simple API tokens will work just well.

*Be careful!* Once you enabled SSL on your API don't allow unsecured connections (e.g. plain http connections).

### Gzip

Up to 80% bandwidth saving can be achieved when using gzip compression (according to Twitter and Stack Exchange). Although this requires additional CPU time to (un)compress the results, the trade-off with network costs usually makes it very worthwhile.

Most frameworks provide an easy way to setup gzip compression and most of the time you can also configure a minimum response size which the compression is enabled on. This way you don't overload the CPU with compressing tiny chuncks of responses.

In order to receive a gzip-encoded response you must do two things: Set an `Accept-Encoding` header, and modify your user agent to contain the string gzip. Here is an example of properly formed HTTP headers for enabling gzip compression:

```
Accept-Encoding: gzip
User-Agent: my program (gzip)
```

