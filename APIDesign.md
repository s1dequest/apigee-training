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

