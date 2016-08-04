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

1. Treat it like a sub-resource and modify it with CRUD operations

**You should never ever** leak any implementation details via your API.
