# Apigee
## Course 1: API Design and Fundamentals of Google Cloud's Apigee API Platform
#### This README and repository will serve as my notes document for all Apigee training coursework.

* Fundamentals:
```
We open apps, click around, and get responses. We may also do things like log into our banking app and make payments. All of these pass information from the app to the API management platform that directs the request to the correct location in the company's backend systems, to then send a response all the way back through the API management platform to your smartphone app.
```
  * The idea is to have centralized API management service for every api consumer that can secure, transform, analyze, scale, publish, and monetize api's via the use of api proxies.
    * Example consumers:
      * Partner apps
      * Consumer apps
      * Employee apps
      * Cloud apps
      * Legacy apps
      * IoT
  * Apigee Edge
    * 'Services layer' (API, Developer, Analytics), example API services include:
      * API Gateway
      * Policies and Programmability
      * OAuth and Security
      * Versioning and Governance
  * Apigee Sense
    * 'Intelligent behavior detection and security product'
    * Protects api's from attacks
    * Alerts administrators to suspicious activity like content scraping, credential stuffing, compromised secrets, etc...
  * Apigee Monetization
    * End to end monetization capability
    * Various revenue options

* Tech Stack:
  * Apigee sits between the client and the server.
  * Gateway handles ingress/egress, routing to API/Dev/Analytics services, etc...
  * Horizontally scalable.
    * Add a load balancer between the client and the gateway, and scale the # of gateways as necessary.
  * Highly available
    * Can span zones/regions.
  * Within the API Services...
    * Router            - handles all incoming api traffic and dispatches it to the message processor.
    * Message Processor - executes all the policies for a given/specific organization and environment.
    * Management Server - provides api's for all configs and management tasks.
    * Enterprise UI     - offers 'extended capability'.
    * cassandra         - stores app config, api keys, and oauth tokens.
    * zookeeper         - contains service config data.
    * openldap          - contains the automization of users and their roles.
  * Within the Analytics Services...
    * Qpidd queuing      - transports the analytics data, postgres server, postgreSQL db to manage the analytics database.
    * Qpid/Ingest Server - ^
  * Within the Dev Services...
    * Dev Portal & MySQL DB - mainly used to expose the API docs, and register external devs and their apps.

* API Lifecycle
  * Designed using openAPI methodology.
    * Devs can upload the api specs which will generate the api proxies.
  * ... Some extra details here I didn't take notes on.
  * API debug tool resembling Postman.
  * Security: PCI and HIPAA Compliance. Pen Testing.
  * Publish: API Portal integration into established company portals.
    * Testable documentation and version management.
  * Scale: "Ops Excellence, HA, Multi-Region, Zero Downtime Deployments"
  * Monitor: Proxy usage and performance allows apigee to find and alert teams that are improperly consuming api's (like OMC for TCN) so as to educate them on how to do it properly.
    * Integrate data via API's to tools like Splunk.
  * Monetize: "Flexible rate plans, internationalization support, usage tracking, limits and notifications."

* API Proxy Flows
  * HTTP Client =(request)=> ProxyEndpoint (PreFlow => Conditional Flows => PostFlow) => TargetEndpoint (PreFlow => Conditional Flows => PostFlow) => Server
  * HTTP Client <= ProxyEndpoint (PreFlow => Conditional Flows => PostFlow, PostClientFlow) => TargetEndpoint (PreFlow => Conditional Flows => PostFlow) <=(response)= Server
  * PreFlow - Common policies for security and traffic mediation here.
  * Conditional Flows - ex: GetAccounts, PostCarts
  * Variables and Conditions:
    * Conditional Flows: `<Condition>request.verb = "GET" and request.path = "/accounts"</Condition>`
    * Policy/Step Conditions:
    ```
    <Step>
      <Name>API Key Validation</Name>
      <Condition>request.verb != "OPTIONS"</Condition>
    </Step>
    ```
    * Flow Vars:
      * Apigee flow vars: `request.path`, `target.url`
      * Customer-defined flow vars: `firebaseJwt`, `firebaseUuid`

* RBAC
  * Org Admin
    * full
  * Business User
    * analytics
  * Ops Admin
    * deployments
  * User/Dev
    * write code
  * Management UI and Management API have the same capabilities.
    * Use the API to write management scripts relating to users, proxies, and env config.
    * Can do similar stuff with the Analtyics API.

### Next, ~/APIDesign.md.
