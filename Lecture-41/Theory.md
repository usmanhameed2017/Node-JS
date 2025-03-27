# HTTP HEADERS & METHODS

### HTTP (Hyper Text Tranfer Protocol)

- We can send data through `HTTP` as plain text, but `HTTPS` adds a security layer that encrypts it.

- HTTP headers are additional pieces of information or metadata in HTTP requests and responses. 

- HTTP headers provide essential information such as content type, content length, content size, and other details that help manage the communication between the client and the server.

- We can also create custom headers by using the `X-` prefix, which is traditionally used to indicate non-standard or application-specific headers.

- Headers can be used for various purposes, including caching, authentication, and authorization.

- In token-based authentication, headers can carry a `bearer token` to verify the user's identity and grant access to protected resources.

### Types Of Headers

- `Request Headers`: These headers are sent from the client side to the server and contain information such as the type of data the client expects or authentication credentials.

- `Response Headers`: These headers are sent from the server side to the client and provide details about the response, such as content type, server information, or caching instructions.

- `Representation Headers`: These headers describe the format or encoding of the data being transferred, such as content encoding (e.g. gzip) or content-type (e.g. JSON, HTML).

- `Payload Headers`: These headers contain the actual data being sent in the request or response body, such as content length, content type, or other metadata related to the payload.

### HTTP Methods

- `GET`: Used to request data from a specified resource. It is safe and does not alter the data and can be repeated without side effects.

- `POST`: Used to send data to the server, typically to create or submit new resources.

- `PUT`: Used to update or replace a current resource/object with new data.

- `PATCH`: Similar to PUT but used for making partial updates to an existing part of a resource/object.

- `DELETE`: Used to delete a specified resource from the server.

- `HEAD`: Similar to GET, but it only requests the headers of a resource, without the actual body content.

- `OPTIONS`: Used to describe the communication options for the target resource, such as which HTTP methods are allowed. Typically used for CORS (Cross-Origin Resource Sharing) preflight requests.

- `TRACE:` Used to perform a diagnostic trace of the path taken by a request to the server.

### HTTP Status Codes

- `100 - 199`: Information messages

- `200 - 299`: Success messages

- `300 - 399`: redirectional messages

- `400 - 499`: Client-side error messages

- `500 - 599`: Server-side error messages

### Status Codes Overview

- `100`: Continue

- `102`: Processing

- `200`: OK

- `201`: Created

- `202`: Accepted

- `307`: Temporary redirect

- `308`: Permanent redirect

- `400`: Bad request

- `401`: Unauthorized

- `402`: Payment required

- `404`: Not found

- `500`: Internal server error

- `504`: Gateway timeout