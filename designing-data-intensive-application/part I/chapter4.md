### chapter 4
#### Encoding and Evolution

**encoding** --> in-memory data structure to a sequence of byte that can be compressed or not.
**service**   --> the API exposed by the server is known as service
**web service** --> when HTTP is used as the underlying protocol for talking to a service, it is called a *web server*
**middleware** --> the service in between, that gets a HTTP call and pass it on another service to accomplish the goal of the request
**REST** --> REST is not a protocol, but rather a design philosophy that builds upon the principle of HTTP


- When HTTP is used as the underlying protocol for talking to the service, it is called a web service. This is perhaps a slight misnomer, because web services are not only used on the web, but in several different contexts. For example:
    - A client application running on a user’s device (e.g., a native app on a mobile device, or JavaScript web app using Ajax) making requests to a service over HTTP. These requests typically go over the public internet.
    - One service making requests to another service owned by the same organization, often located within the same datacenter, as part of a service-oriented/microser‐ vices architecture. (Software that supports this kind of use case is sometimes called middleware.)
    - One service making requests to a service owned by a different organization, usu‐ ally via the internet. This is used for data exchange between different organiza‐ tions’ backend systems. This category includes public APIs provided by online services, such as credit card processing systems, or OAuth for shared access to user data.
- There are two popular approaches to web services: **REST** and **SOAP**

##### REST:
- REST is not a protocol, but rather a design philosophy that builds upon the principles of HTTP. It emphasizes simple data formats, using URLs for identifying resources and using HTTP features for cache control, authentication, and content type negotiation. REST has been gaining popularity compared to SOAP, at least in the context of cross-organizational service integration, and is often associated with microservices. An API designed according to the principles of REST is called RESTful.

##### SAOP:
- By contrast, SOAP is an XML-based protocol for making network API requests.vii Although it is most commonly used over HTTP, it aims to be independent from HTTP and avoids using most HTTP features. Instead, it comes with a sprawling and complex multitude of related standards (the web service framework, known as WS-*) that add various features. The API of a SOAP web service is described using an XML-based language called the Web Services Description Language, or WSDL. WSDL enables code generation so that a client can access a remote service using local classes and method calls (which are encoded to XML messages and decoded again by the framework). This is useful in statically typed programming languages, but less so in dynamically typed ones 

