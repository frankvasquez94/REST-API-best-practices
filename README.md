# REST API design best practices

## Content

- [Objectives](#objectives)
- [Introduction](#introduction)
- [Designing URIs](#designing-uris)
  - [URI format design](#uri-format-design)
  - [URI authority desing](#uri-authority-desing)
  - [Resources modeling](#resources-modeling)
  - [URI path design](#uri-path-design)
  - [URI query desing](#uri-query-desing)
- [Interaction designs with HTTP](#interaction-designs-with-http)
  - [Request Methods](#request-methods)
  - [Response Status Code](#response-status-code)
- [Metadata design](#metadata-design)
  - [HTTP Headers](#http-headers)
  - [Media Types](#media-types)
  - [Registered Media Types](#registered-media-types)
- [Representation design](#representation-design)
  - [Message body format design](#message-body-format-desing)
  - [Media type representation](#media-type-representation)
  - [Error representation design](#error-representation-design)
- [API versioning](#api-versioning)
  - [When to create a version of the API](#when-to-create-a-version-of-the-api)
  - [Why should you do versioning?](#why-should-you-do-versioning)
  - [API versioning types](#api-versioning-types)
- [Security](#security)
  - [Ways to secure a REST API](#ways-to-secure-a-rest-api)
    - [1. Basic authentication](#1-basic-authentication)
    - [2. API Keys](#2-api-keys)
    - [3. OAuth 2](#3-oauth-2)
    - [4. Json Web Token (JWT)](#4-json-web-token-jwt)
- [Design principles](#design-principles)

## Introduction

The REST architectural style is commonly applied to the design of APIs for modern web services. A Web API conforming to the REST architectural style is a REST API. Having a REST API makes a web service “RESTful.” A REST API consists of an assembly of interlinked resources. This set of resources is known as the REST API’s resource model.

Well-designed REST APIs can attract client developers to use web services. In today’s open market where rival web services are competing for attention, an aesthetically pleasing REST API design is a must-have feature.

For many of us, designing a REST API can sometimes feel more like an art than a science. Some best practices for REST API design are implicit in the HTTP standard, while other pseudo-standard approaches have emerged over the past few years. Yet today, we must continue to seek out answers to a slew of questions, such as:

- When should URI path segments be named with plural nouns?

- Which request method should be used to update resource state?

- How do I map non-CRUD operations to my URIs?

- What is the appropriate HTTP response status code for a given scenario?

- How can I manage the versions of a resource’s state representations?

- How should I structure a hyperlink in JSON?

This guide presents a set of API REST design rules to provide a clear and consice answers to the questions listed above. The rules are here to help you design REST APIs with consistency that can be leveraged by the clients that use them.

## Objectives

1. Understand why REST API should be designed and configured, not coded.
2. Learn about design rules for addressing resources with URIs.
3. Apply correctly HTTP methods and HTTP response status codes.
4. Work with metadata throught HTTP headers and media types.

## Designing URIs

REST APIs use Uniform Resource Identifiers (URIs) to address resources.

### URI format design

The rules presented in this section pertain to the format of a URI. RFC 3986 defines the generic URI syntax as shown below:

```text
URI = scheme "://" authority "/" path ["?" query] ["#" fragment]
```

Example:

```text
http://api.canvas.restapi.org/shapes/polygons/quadrilaterals/squares
```

#### **Rule:** A trailing forward slash (/) should not be included in URIs

As the last character within a URI’s path, a forward slash (/) adds no semantic value and may cause confusion. REST APIs should not expect a trailing slash and should not include them in the links that they provide to clients.

Many web components and frameworks will treat the following two URIs equally:

```text
http://api.canvas.restapi.org/shapes/
http://api.canvas.restapi.org/shapes
```

However, every character within a URI counts toward a resource’s unique identity. Two different URIs map to two different resources.

#### Rule: Hyphens (-) should be used to improve the readability of URIs

Example:

```text
http://api.example.restapi.org/blogs/mark-masse/entries/this-is-my-first-post
```

#### Rule: Underscores (_) should not be used in URIs

Sometimes Underlining URIs, depending on the application’s font, the underscore (_) character can either get partially obscured or completely hidden. To avoid this confusion, use hyphens (-) instead of underscores as described above.

#### Rule: Lowercase letters should be preferred in URI paths

RFC 3986 defines URIs as case-sensitive except for the scheme and host components. For example:

```text
http://api.example.restapi.org/my-folder/my-doc  
HTTP://API.EXAMPLE.RESTAPI.ORG/my-folder/my-doc  
http://api.example.restapi.org/My-Folder/my-doc
```

The First and the second URI are the same. The third URI is not the same as URIs 1 and 2, which may cause unnecessary confusion.

#### Rule: File extensions should not be included in URIs

URIs should rely on the media type, as communicated through the Content-Type header, to determine how to process the body’s content. For more about media types

```text
http://api.college.restapi.org/students/3248234/transcripts/2005/fall.json
```

File extensions should not be used to indicate format preference.

```text
http://api.college.restapi.org/students/3248234/transcripts/2005/fall 
```

REST API clients should be encouraged to utilize HTTP’s provided format selection mechanism, the Accept request header.

```text
Accept: application/json
```

Using media type negotiation clients can select a format.

### URI authority desing

#### Rule: Consistent subdomain names should be used for your client developer portal

The full domain name of an API should add a subdomain named api. For example:

```text
http://api.soccer.restapi.org
```

If an API provides a developer portal, by convention it should have a subdomain labeled developer. For example:

```text
http://developer.soccer.restapi.org
```

### Resources modeling

#### Collection

The URI path conveys a REST API’s resource model, with each forward slash separated path segment corresponding to a unique resource within the model’s hierarchy. For example, this URI design:

```text
http://api.soccer.restapi.org/leagues/seattle/teams/trebuchet
```

Indicates that each of these URIs should also identify an addressable resource:

```text
http: //api.soccer.restapi.org/leagues/seattle/teams
http: //api.soccer.restapi.org/leagues/seattle
http: //api.soccer.restapi.org/leagues
http: //api.soccer.restapi.org
```

We call each one of this resources as a **collection**

Resource modeling is similar to the data modeling for a relational database or modeling an object-oriented system.

#### Controller

A controller resource models a procedural concept. Controller resources are like executable functions, with parameters and return values; inputs and outputs.

REST API relies on controller resources to perform application-specific actions that cannot be logically mapped to one of the standard methods (create, retrieve, update, and delete, also known as CRUD).

Controller names typically appear as the last segment in a URI path, with no child resources to follow them in the hierarchy. The example below shows a controller resource that allows a client to resend an alert to a user:

```text
POST /alerts/245743/resend
```

### URI path design

Assigning meaningful values to each path segment helps to clearly communicate the hierarchical structure of a REST API’s resource model design.

#### Rule: A plural noun should be used for collection names

Example:

```text
http://api.soccer.restapi.org/leagues
```

#### Rule: A verb or verb phrase should be used for controller names

Like a computer program’s function, a URI identifying a controller resource should be named to indicate its action. For example:

```text
http: //api.college.restapi.org/students/morgan/register
http: //api.example.restapi.org/lists/4324/dedupe
http: //api.ognom.restapi.org/dbs/reindex
```

#### Rule: Variable path segments may be substituted with identity-based values

The URI Template syntax allows designers to clearly name both the static and variable segments. A URI template includes variables that must be substituted before resolution.  The URI template example below has three variables (leagueId, teamId, and playerId):

```text
http: //api.soccer.restapi.org/leagues/{leagueId}/teams/{teamId}/players/{playerId}
```

#### Rule: CRUD function names should not be used in URIs

HTTP request methods should be used to indicate which CRUD function is performed.

Example:

```text
DELETE /users/1234
```

The following are antipatterns:

```text
GET /deleteUser?id=1234
GET /deleteUser/1234
DELETE /deleteUser/1234
POST /users/1234/delete
```

### URI query desing

Optional **query** comes after the path and before the optional fragment:

```text
URI = scheme "://" authority "/" path ["?" query ["#" fragment]
```

#### Rule: The query component of a URI may be used to filter collections or stores

In the next example, the response message’s state representation contains a listing of all the users in the collection:

```text
GET /users
```

In the next example, the response message’s state representation contains a filtered list of all the users in the collection with a “role” value of admin:

```text
GET /users?role=admin
```

#### Rule: The query component of a URI should be used to paginate collection or store results

A REST API client should use the query component to paginate collection and store results with the pageSize and pageStartIndex parameters. The pageSize parameter specifies the maximum number of contained elements to return in the response. The pageStartIndex parameter specifies the zero-based index of the first element to return in the response. For example:

```text
GET /users?pageSize=25&pageStartIndex=50
```

We can consider designing a special controller when the complexity of a client's pagination or filtering exceeds  the simple formating capabilities of the quuery part.

Example of a special controller:

```text
POST /users/search
```

## Interaction designs with HTTP

### Request Methods

Clients specify the desired interaction method in the Request-Line part of an HTTP request message.

The purpose of GET is to retrieve a representation of a resource’s state. HEAD is used to retrieve the metadata associated with the resource’s state. PUT should be used to add a new resource to a store or update a resource. DELETE removes a resource from its parent. POST should be used to create a new resource within a collection and execute controllers.

### Rules

#### **Rule:** GET and POST must not be used to tunnel other request methods

Tunneling refers to any abuse of HTTP that masks or misrepresents a message’s intent and undermines the protocol’s transparency. A REST API must not compromise its design by misusing HTTP’s request methods in an effort to accommodate clients with limited HTTP vocabulary. Always make proper use of the HTTP methods

#### **Rule:** GET must be used to retrieve a representation of a resource

A REST API client uses the GET method in a request message to retrieve the state of a resource, in some representational form. A client’s GET request message may contain headers but no body.

The architecture of the Web relies heavily on the nature of the GET method. Clients count on being able to repeat GET requests without causing side effects.

#### **Rule:** HEAD should be used to retrieve response headers

Clients use HEAD to retrieve the headers without a body. In other words, HEAD returns the same response as GET, except that the API returns an empty body. Clients can use this method to check whether a resource exists or to read its metadata.

```text
Like GET, a HEAD request message may contain headers but no body.
```

#### **Rule:** PUT must be used to both insert and update a stored resource

PUT must be used to add a new resource to a store, with a URI specified by the client. PUT must also be used to update or replace an already stored resource.

The PUT request message must include a representation of a resource that the client wants to store. However, the body of the request may or may not be exactly the same as a client would receive from a subsequent GET request. For example, a REST API’s store resource may allow clients to include only the mutable portions of the resource state in the request message’s representation.

#### **Rule:** PUT must be used to update mutable resources

Clients must use the PUT request method to make changes to resources. The PUT request message may include a body that reflects the desired changes.

#### **Rule:** POST must be used to create a new resource in a collection

Clients use POST when attempting to create a new resource within a collection. The POST request’s body contains the suggested state representation of the new resource to be added to the server-owned collection.

```text
This is the first of two uses of the POST method within the context of REST API design. Metaphorically, this use of POST is analogous to “posting” a new message on a bulletin board.
```

#### **Rule:** POST must be used to execute controllers

Clients use the POST method to invoke the function-oriented controller resources. A POST request message may include both headers and a body as inputs to a controller resource’s function.

Our REST API designs use POST, along with a targeted controller resource, to trigger all operations that cannot be intuitively mapped to one of the other core HTTP methods. In other words, the POST method should not be used to get, store, or delete resources—HTTP already provides specific methods for each of those functions.

```text
This is the second use of POST in the design of REST APIs. This use case resembles the fairly common concept of a runtime system’s “PostMessage” mechanism, which allows functions to be invoked across some sort of boundary.
```

#### **Rule:** DELETE must be used to remove a resource from its parent

A client uses DELETE to request that a resource be completely removed from its parent, which is often a collection or store. Once a DELETE request has been processed for a given resource, the resource can no longer be found by clients. Therefore, any future attempt to retrieve the resource’s state representation, using either GET or HEAD, must result in a 404 (“Not Found”) status returned by the API.

```text
If an API wishes to provide a “soft” delete or some other state-changing interaction, it should employ a special controller resource and direct its clients to use POST instead of DELETE to interact.
```

#### **Rule:** OPTIONS should be used to retrieve metadata that describes a resource’s available interactions

Clients may use the OPTIONS request method to retrieve resource metadata that includes an Allow header value. For example:

```text
Allow: GET, PUT, DELETE
```

In response to an OPTIONS request, a REST API may include a body that includes further details about each interaction option.

### Response Status Code

#### HTTP response **success** code

|Code|Name|Meaning|
|:----|:----|:----|
|200|OK|Indicates a nonspecific success|
|201|Created|Sent primarily by collections and stores but sometimes also by controllers, to indicate that a new resource has been created|
|202|Accepted|Sent by controllers to indicate the start of an asynchronous action|
|204|No Content|Indicates that the body has been intentionally left blank|
|301|Moved Permanently|Indicates that a new permanent URI has been assigned to the client’s requested resource|
|303|See Other|Sent by controllers to return results that it considers optional|
|304|Not Modified|Sent to preserve bandwidth (with conditional GET)|
|307|Temporary Redirect|Indicates that a temporary URI has been assigned to the client’s requested resource|

#### HTTP response **error** code

|Code|Name|Meaning|
|:----|:----|:----|
|400|Bad Request|Indicates a nonspecific client error|
|401|Unauthorized|Sent when the client either provided invalid credentials or forgot to send them|
|402|Forbidden|Sent to deny access to a protected resource|
|404|Not Found|Sent when the client tried to interact with a URI that the REST API could not map to a resource|
|405|Method Not Allowed|Sent when the client tried to interact using an unsupported HTTP method|
|406|Not Acceptable|Sent when the client tried to request data in an unsupported media type format|
|409|Conflict|Indicates that the client attempted to violate resource state|
|412|Precondition Failed|Tells the client that one of its preconditions was not met|
|415|Unsupported Media Type|Sent when the client submitted data in an unsupported media type format|
|500|Internal Server Error|Tells the client that the API is having problems of its own|


## Metadata design

### HTTP Headers

Various forms of metadata may be conveyed through the entity headers contained within HTTP’s request and response messages. HTTP defines a set of standard headers, some of which provide information about a requested resource. Other headers indicate something about the representation carried by the message. Finally, a few headers serve as directives to control intermediary caches.

#### **Rule:** Content-Type must be used

The Content-Type header names the type of data found within a request or response message’s body. The value of this header is a specially formatted text string known as a media type, which is the subject of Media Types. Clients and servers rely on this header’s value to tell them how to process the sequence of bytes in a message’s body.

#### **Rule:** Content-Length should be used

The Content-Length header gives the size of the entity-body in bytes. In responses, this header is important for two reasons. First, a client can know whether it has read the correct number of bytes from the connection. Second, a client can make a HEAD request to find out how large the entity-body is, without downloading it.

#### **Rule:** Last-Modified should be used in responses

The Last-Modified header applies to response messages only. The value of this response header is a timestamp that indicates the last time that something happened to alter the representational state of the resource.

#### **Rule:** ETag should be used in responses

The value of ETag is an opaque string that identifies a specific “version” of the representational state contained in the response’s entity. The entity is the HTTP message’s payload, which is composed of a message’s headers and body. The entity tag may be any string value, so long as it changes along with the resource’s representation. 

Clients may choose to save an ETag header’s value for use in future GET requests, as the value of the conditional If-None-Match request header. If the REST API concludes that the entity tag hasn’t changed, then it can save time and bandwidth by not sending the representation again.

#### **Rule:** Stores must support conditional PUT requests

A store resource uses the PUT method for both insert and update, which means it is difficult for a REST API to know the true intent of a client’s PUT request. Through headers, HTTP provides the necessary support to help an API resolve any potential ambiguity. A REST API must rely on the client to include the `If-Unmodified-Since` and/or `If-Match` request headers to express their intent. The `If-Unmodified-Since` request header asks the API to proceed with the operation if, and only if, the resource’s state representation hasn’t changed since the time indicated by the header’s supplied timestamp value. The `If-Match` header’s value is an entity tag, which the client remembers from an earlier response’s ETag header value. The If-Match header makes the request conditional, based upon an exact match of the header’s supplied entity tag value and the representational state’s current entity tag value, as stored or computed by the REST API.

#### **Rule:** Location must be used to specify the URI of a newly created resource

The Location response header’s value is a URI that identifies a resource that may be of interest to the client. In response to the successful creation of a resource within a collection or store, a REST API must include the Location header to designate the URI of the newly created resource.

#### **Rule:** Cache-Control, Expires, and Date response headers should be used to encourage caching

Caching is one of the most useful features built on top of HTTP. You can take advantage of caching to reduce client-perceived latency, to increase reliability, and to reduce the load on an API’s servers.

When serving a representation, include a Cache-Control header with a max-age value (in seconds) equal to the freshness lifetime. For example:

```
Cache-Control: max-age=60, must-revalidate
```

To support legacy HTTP 1.0 caches, a REST API should include an Expires header with the expiration date-time. The value is a time at which the API generated the representation plus the freshness lifetime. REST APIs should also include a Date header with a date-time of the time at which the API returned the response.y

```
Date: Tue, 15 Nov 1994 08:12:31 GMT
Expires: Thu, 01 Dec 1994 16:00:00 GMT
```

#### **Rule:** Cache-Control, Expires, and Pragma response headers may be used to discourage caching

If a REST API’s response must not cached, add Cache-Control headers with the value no-cache and no-store. In this case, also add the Pragma: no-cache and Expires: 0 header values to interoperate with legacy HTTP 1.0 caches.

### Media Types

To identify the form of the data contained within a request or response message body, the Content-Type header’s value references a media type.

### Media Type Syntax
Media types have the following syntax:

```
type "/" subtype *( ";" parameter )
```

The *type* value may be one of: application, audio, image, message, model, multipart, text, or video. A typical REST API will most often work with media types that fall under the application type. In a hierarchical fashion, the media type’s subtype value is subordinate to its type.

Note that parameters may follow the type/subtype in the form of attribute=value pairs that are separated by a leading semi-colon (;) character. A media type’s specification may designate parameters as either required or optional.

The two examples below demonstrate a Content-Type header value that references a media type with a single charset parameter:

```
Content-type: text/html; charset=ISO-8859-4
Content-type: text/plain; charset="us-ascii"
```

### Registered Media Types

The Internet Assigned Numbers Authority (IANA) governs the set of registered media types and provides links to each type’s published specification (RFC).

Some commonly used registered media types are listed below:

* ***text/plain:*** A plain text format with no specific content structure or markup.

* ***text/html:***
Content that is formatted using the HyperText Markup Language (HTML).

* ***image/jpeg:***
An image compression method that was standardized by the Joint Photographic Experts Group (JPEG).

* ***application/xml:***
Content that is structured using the Extensible Markup Language (XML).

* ***application/atom+xml:***
Content that uses the Atom Syndication Format (Atom), which is an XML-based format that structures data into lists known as feeds.

* ***application/javascript:***
Source code written in the JavaScript programming language.

* ***application/json:***
The JavaScript Object Notation (JSON) text-based format that is often used by programs to exchange structured data.

## Representation design

### Message body format desing

A REST API commonly uses a response message’s entity body to help convey the state of a request message’s identified resource. The most commonly used text formats are XML and JSON.

In this section we will focus on json format.

#### Rule: JSON should be supported for resource representation

JSON is a popular format that is commonly used in REST API design. A rest API should use the JSON format to structure its information.

#### Rule: JSON must be well-formed

JSON names should use **camelCase** format and should avoid special characters whenever possible.

we must not use (-) hyphen in our keys, because it complicates parsers. We can use underscore (_) instead, but using lowecase or camelCase is the best.

The JSON object syntax defines names as strings which are always surrounded **by double quotes**.

Another best practice is to create always a **root** element. Create a root element is optional, but it helps when we are working with complicated JSON.

The example below show a well-formed json:

```json
{
    "firstName" : "Osvaldo",
    "lastName" : "Alonso",    
    "number" : 6,  
    "birthDate" : "2005-11-15"  
}
```

Json supports number values directly.
Json does not support dates directly, so they are tipically formated as string.

#### Rule: XML and other formats may optionally be used for resource representation

We have been stablished that Json format should be a supported representation format for REST API. REST APIs may optionally support XML, HTML, and other languages as alternative formats for resource representation. Clients should express their desired representation using media type negotiation.

### Media type representation

#### Field representation

A field is named slot with some associated information that is stored in its value.

We can specify the field representation using the following format:

```text
{
    "type" : <Text, that defines the content of its value>,
    "defaultValue" : <a type-specific value>,
    "readOnly" : <Boolean>,
    "required" : <Boolean>,
    "hidden"   : <Boolean,  indicates whether a REST API should include the field within forms carried by its response messages>,
    "constraints" : <Array of constraints that define the field domain, for example: regex,choices, etc.>,
    "description" : <Text>
}
```

#### JSON Field types

The json fields may be one of the following types:

**String**: Strings in JSON must be written in double quotes.

Example:

```json
{
    "firstName": "John"
}
```

**Number**: Numbers in JSON must be an integer or a floating point.

exaples:

```json
{
    "age": 15,
    "weight": 15.55
}
```

**Object**: Values in JSON can be objects.

Example:

```json
{
    "employee": {
        "firstName": "John",
        "age": 25,
        "salary": 3200
    }
}
```

**Array**: Values in JSON can be arrays.

Example:

```json
{
    "employees": ["Anna", "John"]
}
```

**Boolean**: Values in JSON can be boolean {true | false}

Example:

```json
{
    "onSale": true
}
```

**null**: Values in JSON can be null.

Example:

```json
{
    "midleName": null
}
```

### Error representation design

The first step when we are working with errors is to provide a client with a proper http status code. Additionally we may need to provide more details in the resposne body.

Http's 400 and 500 error status codes should be used with client readable information in the response message’s entity body.

#### Rule: A consistent form should be used to represent errors

This rule describes the reprsentation design for a single error.

The example below show the representation error desing using the Content-type: application/json:

```json
{
    "code": "123",
    "description": "an error has  occurred"
}
```

**code**: Represents the unique error identifier.
**description**: Represents an optional error description.

#### Rule: A consistent form should be used to represent error responses

If a request result in one or more errors we should use an ErrorContainer to represent a list of errors.

In the example below there is a ErrorContainer using the Media-Type: application/json.

```json
{
    "errors": [
        {
            "code": "123",
            "description": "this field is required"
        },
        {
            "code": "124",
            "description": "Failed to update /users/1234"
        }
    ]
}
```

#### Rule: Consistent error types should be used for common error conditions

Generic error types may be leveraged by many APIs. These error types should be defined once and then shared across all APIs.

## API versioning

 It's critical that clients can count on services to be stable over time, and it's critical that services can add features and make changes.

 API versioning is a procedure that implicates making operational and structural changes to the APIs so that they can become adaptable and suitable to the growing and altering requirements of the customers. In other words API versioning is the practice of transparently managing changes to the APIs.

### When to create a version of the API

1. API payload structures are changed at any level
2. Any API endpoint is eliminated to fix the design flaws or get rid of a faulty HTTP implementation.

Additionally to the above reasons, versioning is a **must** when developers will add new API endpoints or parameters to the API responses. However, both these situations won’t ask for a completely new version release. Only minor updates are enough.

### Why should you do versioning?

Using the version API is cricial in two aspects:

1. ‍**Disruptive change**

Adjusting any code or primal API structure will direct to breaking changes on the consumer side as well. If one doesn’t adopt the approach, the end-user might compel the consumer to make changes in their applications as well.

Here are some scenarios that falls under disruptive changes:

- Altering the original request/response format type (e.g from json to xml).
- Revising the data type of a property (e.g from integer to string) or the property name completely (e.g. from name to productName).
- Addition of a new essential field, e.g. a **required** header or property in the body.
- Eliminating any pre-defined response property like description details (e.g removing description from product).

All this changes have a direct impact on the applications designed using the original APIs.

2. **Change management in the API**

Any big or notable change should be handled through versioning only. Change management, ensure that it happens.

The process is feasible to avert any breaking API changes that you can expect. It revolves around the below principles:

- Providing ongoing support for the present API properties/end-points
- Providing fresh end-points or properties instead of modifying the previous one
- Carefully terminating the outdated end-points/properties

Adhering to these principles help reduce the efforts invested in versioning and make it simpler to deploy.

### API versioning types

1. **Query parameter versioning**

As the technique name denotes we use a query parameter to indicate the API version. It is effective and easy to implement. We can define a default version.

Example:

```text
https://api.contoso.com/products/users?version=1.0
```

2. **URI Versioning**

It involves mentioning the API’s version in URI. It’s loved because of its unmatched effectiveness and easy implementation. One has to introduce the ‘v’ prefix in the URI path.

The only thing that you need to bring into action is setting API endpoints every API developer does eventually. It’s perfect on most of the fronts but disappoints by causing a major API design flaw. It forces every URI to feature a distinct resource that causes a huge URI footprint.

Example:

```text
https://api.contoso.com/v1.0/products/users
```

3. ‍**Custom Headers Versioning**

It permits you to declare the API version in a header, letting you complete the process fast.

```text
--header 'version=1.0'
```

## Security

Secure a REST API is one of the biggest challenges to solve. There are different reasons why REST API security is quite important:

1. To protect data.
2. To protect against attacks.
3. Anti-Farming.

### Ways to secure a REST API

RESTful APIs are stateless, so the auth security must not depend on the session or cookie alone, these auth parameters should be validated on each and every request to the server most likely using the headers. Bellow is a list of the most useful ways to secure REST APIs:

#### 1. Basic authentication

One of the simplest way to implement security in REST APIs. It does not require any cookies or sessions, it just passes auth credentials through the HTTP headers.

So it involves the client sending user and password seperated by a single colon encoded with Base64 all together in a string.

Example:

```text
apiUser:pass123
```

Authorization: Basic YXBpVXNlcjpwYXNzMTIz

This method is not very secure, the most serious flaw with basic auth is that you pass user and password over the network in the header in the form of encoded string.

But Still if you are using this technique then it should be used with TLS or SSL (https) protocol in order to protect sensitive information.

#### 2. API Keys

API keys are for projects or applications, authentication is for users. For Example, AWS exposing API Keys to access it and identify the application.

While API keys identify the calling project, they don’t identify the calling user. If we have created an application that is calling an API, an API key can identify the application that is making the call, but not the identity of the person who is using the application.

**when to use API Keys:**

- You do want to block anonymous traffic. API keys identify an application’s traffic for the API producer.
- You want to control the number of calls made to your API.
- You want to identify usage patterns in your API’s traffic.

**When not to use API Keys:**

- Identifying individual users, API keys don’t identify users, they identify projects.

#### 3. OAuth 2

The OAuth 2.0 is an authorization framework that enables a third-party application to obtain limited access to an HTTP service, either on behalf of a resource owner or by allowing the third-party application to obtain access on its own behalf.

OAuth works over HTTPS and authorizes devices, APIs, applications and servers with access tokens rather than credentials.

OAuth 2.0 can be used to read data of a user from another application without compromising the user’s sensitive data, like user credentials. It also supplies the authorization workflow for web, desktop applications, and mobile devices.

**OAuth Tokens:**

The OAuth uses tokens, to authorize user. Access Tokens and the refresh tokens.

**Access Tokens:**

Are the tokens used by the client to access the resource API, they have an expiry. This is not something used by sercret clients but are available with public clients.

**Refresh Tokens:**

These tokens can live much longer like in days,months,years. This token is used to get new access tokens. To get a Refresh tokens applications typically need secret clients with authentication.

Example:

```text
Authorization: Bearer 0123456789abcxyz
```

#### 4. Json Web Token (JWT)

It is one of the Token authentication standard. It allows you to digitally sign information(called as claims) with a signature which can be verified later with a secret signing key.

JWT can store any type of data, which like OAuth access tokens should be passed in the authorization header.

Authorization: Bearer JWT

Example:

```text
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

## Design principles
