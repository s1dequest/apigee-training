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
