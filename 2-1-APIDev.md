## Course : API Development on Google Cloud's Apigee API Platform
### Building an API Proxy
`A Proxy acts as an intermediary for requests from clients seeking resources from other servers.`

* Client <=> Proxy <=> Server
  * The client does not know anything about the server on the other end. All the client knows is that they sent the request through a proxy (and the proxy, hopefully, routed it where it needs to go).
  * Reverse Proxy:
    * Takes requests from the internet and forwards them to servers in an internal network.
    * Requests from outside the internal network may or may not know about the internal networks existence.
  * In apigee, its basically the same thing:
    * **App <=secure=> API Proxy <=> API**
    * Client <=> Proxy Endpoint | Target Endpoint <=> Server

* Why add the extra proxy hop that Apigee requires?
  * Gain control and insight into the inbound API requests.
    * Verify tokens
    * Collect analytics
    * Serve requests from cache
    * Perform traffic management
  * Decouple the dev-facing api from the api exposed by backend services.

* Types of Proxy Flows and Rules:
  * Note: Each 'step' in the proxy configuration is a policy.
  * Flows, all of which can contain various policies
    * Preflow - useful when you need to make sure certain code executes before anything else happens.
      * Ex: rate limiting policies
    * Conditional Flow
    * Postflow
      * Ex: Mediation policies
    * PostClient Flow
      * Ex: (ONLY) Logging messages after a response is returned to the client.
  * Rules
    * Route Rules - define which backend target to route to.
    * Fault Rules - what happens when a policy fails or when the target returns an error.

* Advanced Endpoint Props
  * ProxyEndpoint (handles req and res to client) `<HTTPProxyConnection>`
    * `request.streaming.enabled`
    * `response.streaming.enabled`
  * TargetEndpoint (handles req and res to the backend) `<HTTPTargetConnection>`
    * `connect.timeout.millis`
    * `io.timeout.millis`
    * `request.streaming.enabled`
    * `reponse.streaming.enabled`
    * `success.codes`

* Debugging Using Trace:
  * Shows each step your api takes in request and response.

* Lab: Building an API Proxy
  * Creating an API Proxy for a backend service that you want to expose requires you to provide to Apigee Edge:
    * the base network address for the backend service,
    * the HTTP verbs and resource paths you would like to expose to "consumer developers",
    * a few other bits and bobs of information
  * After following the directions to build and deploy the proxy...
    * In Edge, go to API Proxies > Products > Trace
    * Select 'Start Trace Session'
    * Send a request to `/v1/product/{product_id}` in Postman
    * The UI in Edge should now update with the transaction map and all other information the Trace tool captures.

* Conditions
  * Results are boolean
  * Chaining possible
  * Format = `<Condition>{var.name}{operator}{"value"}</Condition>`
    * example
    * Here, we only execute the policy if the request header is "application/json"
```xml
<Step>
  <Coniditon>request.header.accept = "application/json"</Condition>
  <Name>XMLToJSON</Name>
</Step>
```
  * Target endpoint route selection example:
```xml
<RouteRule name="xmlTarget">
  <Coniditon>request.header.Content-Type = "text/xml"</Condition>
  <TargetEndpoint>XmlTargetEndpoint</TargetEndpoint>
</RouteRule>

<RouteRule name="default">
  <TargetEndpoint>target</TargetEndpoint>
</RouteRule>
```
  * Documentation notes a few pattern matching options. Can use Regex.
  * Default route rules should always be last.

* Lab: CORS Preflight Condition
  * "CORS (Cross-origin resource sharing) is a standard mechanism that allows JavaScript XMLHttpRequest (XHR) calls executed in a web page to interact with resources from non-origin domains. Most modern browsers support CORS."
  * "CORS preflight refers to sending a request to a server to verify if it supports CORS. Typical preflight responses include which origins the server will accept CORS requests from, a list of HTTP methods that are supported for CORS requests, headers that can be used as part of the resource request, the maximum time preflight response will be cached, and others. If the service does not indicate CORS support or does not wish to accept cross-origin requests from the client's origin, the cross-origin policy of the browser will be enforced and any cross-domain requests made from the client to interact with resources hosted on that server will fail."

### Policies
* What is a Policy in Apigee?
  * Like a module that implements a specific, limited management function.
  * Programs API behavior
  * Allow users to add custom logic via JS, Python, etc...
  * Ex: Transform a backend XML response to JSON.
* Categories of prebuilt policies:
  * Traffic management
    * Protect against DDOS or severe traffic spikes
    * Used as 'preflow'
    * Spike Arrest:
      * Pre-built policy. Just add from Develop => API Proxies => Products => Develop => Add Step.
      * Limit requests per time interval (200ps for 200 requests per second).
    * Quota:
      * Use 'Reset Quota' to do exactly what you'd expect
      * Can configure different api consumption limits for different roles (product, dev, etc...)
    * Concurrent Rate Limit:
      * Uncommon
      * Throttles inbound connections from Edge to backend
      * Limits concurrent connections
      * Must be attached to both request and response flows in the target endpoint
    * Response Cache:
      * Cache whole HTTP response
      * Improve performance by retrieving response from cache
      * Policy attached in both request and response flows
      * Only used on GET calls
  * Mediation
    * Group of policies that interacts with and have the ability to transform the request in response as your proxy executes. Ex: JSON to XML.
    * Assign Message:
      * Create or Update request, response, and flow vars.
      * Can be used at any point in the proxy.
    * Extract Vars:
      * Extract data from request, response, and flow vars.
      * Use text matching like regex.
      * Can be used at any point in the proxy.
    * Access Entity
      * Retrieve metadata about a consumer at runtime.
      * Ex: Consuming customer email address.
      * Can be used at any point in the proxy.
    * Key Value Map Ops:
      * Allows runtime access to key value maps you have stored in apigee edge.
      * Use with GET, PUT, DELETE.
      * Can be used at any point in the proxy.
  * Security
    * Basic Auth
      * Base64 encode un and pw to be sent to the backend.
      * Base64 decode auth header.
      * Can be used at any point in the proxy.
    * XML, JSON, Regex, Threat Protection
      * Validate request payloads via `Content-type: application/xml` `Content-type: application/json`.
        * Ex: XML Validation validates the xml payload against the provided xml schema. If the schema does not match what we provide, an error is returned.
      * Regex validates URI, Headers, and payload against the patterns specified. Separate from the above XML/JSON validation.
    * Verify API Key Policy
      * Returns 401 if key is invalid.
      * Verify api key at runtime.
    * OAuth v2
      * Generate access token, verify token, gen auth code, refresh access token.
      * Get and set token attributes
      * Delete codes and tokens
      * Can be used at any point in the proxy.
    * SAML
      * For SOAP or XML payloads.
    * Access Control
      * IP Whitelisting
  * Extension
