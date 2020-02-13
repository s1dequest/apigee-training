## Course 1: API Design and Fundamentals of Google Cloud's Apigee API Platform
### API Design & Best Practices
`Consumption model, REST, proper versioning, and response codes.`

* Basics of REST
  * 'Representational State Transfer'
  * REST has a set of contraints, namely:
    * HATEOAS - Hypermedia as the Engine of App State
      * Response payloads should include hyperlinks instructing the dev app on what action can be performed next
  * We will use 'Pragmatic' REST
    * 'outside-in view of how to design APIs'
    * uses standard HTTP concepts to access machine readable resources.
    * easily understandable, readily consumbable, self-documenting (Swagger).
    * allows access to APIs with very little overhead.
    * What does it look like?
      * HTTP Verb Driven:
        * GET, POST, PUT, DELETE
      * Resource Oriented
        * /v1/customers = collection of or search for customers
        * /v1/customers/{id} = specific customer
    * Sample interaction:
      * `GET /v1/customers HTTP/1.1 on Host: api.yourdomain.com`
        * Returns a json array of customers
        * Specifying a specific customer id would return a json object with that customers information.
        * Alternatively, if the endpoint has been moved, the response object should tell the api consuming dev what to do. Ex:
        ```
        301 Moved Permanently
        Location: https://api.yourdomain.com/v2/customers
        {
          "depracated": true,
          "message": "use /v2/customers"
        }
        ```

* Best Practices of API Design
  * Outside-In approach - Consumption Model.
    * "If I was an app developer consuming this API, how would I want it to work?"
  * Allow consumers to give feedback.
  * Focus on thinking about resources that are used rather than actions that are taken.
    * Nouns are good. Verbs are bad.
  * Do any existing services provide most of the functions you need?
  * Non-functional requirements - SLAs, latency timing, etc... should not be allowed to impact API design.
  * Avoid anything custom that cannot be re-used and must be explained.
  * Plural Nouns = Collections
  * Primary Resources should be no greater than 2 levels deep into an endpoint.
    * ex: `https://myserver/v1/dogs/{dog-id}`
  * Verbs should not be a part of URI's.
    * GET, POST, PUT, DELETE on a resource `.../dogs/...` should cover everything you need.

* Response Codes and Pagination
  * Sending response codes is a balancing act between not sending an untrusted actor data/information they can use to further dig into your system, and sending developers what they need to correct an issue.
  * Ex:
  ```
  GET /users/U123 => 404 Not Found
  GET /users/U124 => 403 Forbidden
  ```
    * This response would tell a hacker that `U124` exists.
    * Better solution is send `404 Not Found` to all invalid data requests.
  * Common Status Codes:
    * 200 OK, 201 Created, 304 Not Modified, 400 Bad Request, 401 Unauthorized, 404 Not Found, 500 Server Error.
  * Responses should not include any information (like verisoning of backend systems) that would give a hacker insight into possible attack metrics.
    * Good response example:
```json
// 400 Bad Request
{
  "errorCode": "400.01",
  "errorMessage": "Missing required params in payload",
  "docsLink": "https://link.to.docs/api/theResource"
  }
```
  * Paginate any response with a list of objects of the same type.
    * Ex:
``` json
{
  "pagination": {
    "page": 4,
    "pageSize": 10,
    "items": 10,
    "next":"https://.../resource?page=5",
    "prev":"https://.../resource?page=3",
  },
  "items": [
    {
      "id": "something"
    },{/*...*/},{/*...*/}
  ]
}
```
