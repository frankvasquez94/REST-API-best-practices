# REST API design best practices


## Table of content

- [Objectives](#objectives)

- [Introduction](#introduction)

- [Designing URIs](#designing-uris)

- [Interaction designs with HTTP](#interaction-designs-with-http)

- [Metadata design](#metadata-design)

- [Representation design](#representation-design)

- [Client concerns](#client-concerns)

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

### URI format

The rules presented in this section pertain to the format of a URI. RFC 3986 defines the generic URI syntax as shown below:

```
URI = scheme "://" authority "/" path [ "?" query ] [ "#" fragment ]
```
#### Rules

##### **Rule:** Forward slash separator (/) must be used to indicate a hierarchical relationship.

Example: 

```
http://api.canvas.restapi.org/shapes/polygons/quadrilaterals/squares
```

##### **Rule:** A trailing forward slash (/) should not be included in URIs

As the last character within a URI’s path, a forward slash (/) adds no semantic value and may cause confusion. REST APIs should not expect a trailing slash and should not include them in the links that they provide to clients.

Many web components and frameworks will treat the following two URIs equally:

```
http://api.canvas.restapi.org/shapes/
http://api.canvas.restapi.org/shapes
```
However, every character within a URI counts toward a resource’s unique identity. Two different URIs map to two different resources.

##### **Rule:** Hyphens (-) should be used to improve the readability of URIs.

Example:
```
http://api.example.restapi.org/blogs/mark-masse/entries/this-is-my-first-post
```

##### **Rule:** Underscores (_) should not be used in URIs.

Sometimes Underlining URIs, depending on the application’s font, the underscore (_) character can either get partially obscured or completely hidden. To avoid this confusion, use hyphens (-) instead of underscores as described above.

##### **Rule:** Lowercase letters should be preferred in URI paths.

RFC 3986 defines URIs as case-sensitive except for the scheme and host components. For example:

```
http://api.example.restapi.org/my-folder/my-doc  
HTTP://API.EXAMPLE.RESTAPI.ORG/my-folder/my-doc  
http://api.example.restapi.org/My-Folder/my-doc
```

The First and the second URI are the same. The third URI is not the same as URIs 1 and 2, which may cause unnecessary confusion.

##### **Rule:** File extensions should not be included in URIs

URIs should rely on the media type, as communicated through the Content-Type header, to determine how to process the body’s content. For more about media types

```
http://api.college.restapi.org/students/3248234/transcripts/2005/fall.json
```
File extensions should not be used to indicate format preference.

```
http://api.college.restapi.org/students/3248234/transcripts/2005/fall 
```

REST API clients should be encouraged to utilize HTTP’s provided format selection mechanism, the Accept request header.

```
Accept:  application/json
```

Using media type negotiation clients can select a format.



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

```
Like GET, a HEAD request message may contain headers but no body.
```

#### **Rule:** PUT must be used to both insert and update a stored resource

PUT must be used to add a new resource to a store, with a URI specified by the client. PUT must also be used to update or replace an already stored resource.

The PUT request message must include a representation of a resource that the client wants to store. However, the body of the request may or may not be exactly the same as a client would receive from a subsequent GET request. For example, a REST API’s store resource may allow clients to include only the mutable portions of the resource state in the request message’s representation.

#### **Rule:** PUT must be used to update mutable resources

Clients must use the PUT request method to make changes to resources. The PUT request message may include a body that reflects the desired changes.

#### **Rule:** POST must be used to create a new resource in a collection

Clients use POST when attempting to create a new resource within a collection. The POST request’s body contains the suggested state representation of the new resource to be added to the server-owned collection.

```
This is the first of two uses of the POST method within the context of REST API design. Metaphorically, this use of POST is analogous to “posting” a new message on a bulletin board.
```

#### **Rule:** POST must be used to execute controllers

Clients use the POST method to invoke the function-oriented controller resources. A POST request message may include both headers and a body as inputs to a controller resource’s function.

Our REST API designs use POST, along with a targeted controller resource, to trigger all operations that cannot be intuitively mapped to one of the other core HTTP methods. In other words, the POST method should not be used to get, store, or delete resources—HTTP already provides specific methods for each of those functions.

```
This is the second use of POST in the design of REST APIs. This use case resembles the fairly common concept of a runtime system’s “PostMessage” mechanism, which allows functions to be invoked across some sort of boundary.
```

#### **Rule:** DELETE must be used to remove a resource from its parent

A client uses DELETE to request that a resource be completely removed from its parent, which is often a collection or store. Once a DELETE request has been processed for a given resource, the resource can no longer be found by clients. Therefore, any future attempt to retrieve the resource’s state representation, using either GET or HEAD, must result in a 404 (“Not Found”) status returned by the API.

```
If an API wishes to provide a “soft” delete or some other state-changing interaction, it should employ a special controller resource and direct its clients to use POST instead of DELETE to interact.
```

#### **Rule:** OPTIONS should be used to retrieve metadata that describes a resource’s available interactions

Clients may use the OPTIONS request method to retrieve resource metadata that includes an Allow header value. For example:

```
Allow: GET, PUT, DELETE
```

In response to an OPTIONS request, a REST API may include a body that includes further details about each interaction option.

## Metadata design

## Representation design

## Client concerns

## Design principles
