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
