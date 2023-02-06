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

***Rule:*** Forward slash separator (/) must be used to indicate a hierarchical relationship.

Example: 

```
http://api.canvas.restapi.org/shapes/polygons/quadrilaterals/squares
```

**Rule:*** A trailing forward slash (/) should not be included in URIs

As the last character within a URI’s path, a forward slash (/) adds no semantic value and may cause confusion. REST APIs should not expect a trailing slash and should not include them in the links that they provide to clients.

Many web components and frameworks will treat the following two URIs equally:

```
http://api.canvas.restapi.org/shapes/
http://api.canvas.restapi.org/shapes
```
However, every character within a URI counts toward a resource’s unique identity. Two different URIs map to two different resources.

**Rule:** Hyphens (-) should be used to improve the readability of URIs.

Example:
```
http://api.example.restapi.org/blogs/mark-masse/entries/this-is-my-first-post
```

**Rule:** Underscores (_) should not be used in URIs.

Sometimes Underlining URIs, depending on the application’s font, the underscore (_) character can either get partially obscured or completely hidden. To avoid this confusion, use hyphens (-) instead of underscores as described above.

**Rule:** Lowercase letters should be preferred in URI paths.

RFC 3986 defines URIs as case-sensitive except for the scheme and host components. For example:

```
http://api.example.restapi.org/my-folder/my-doc  
HTTP://API.EXAMPLE.RESTAPI.ORG/my-folder/my-doc  
http://api.example.restapi.org/My-Folder/my-doc
```

The First and the second URI are the same. The third URI is not the same as URIs 1 and 2, which may cause unnecessary confusion.

**Rule:** File extensions should not be included in URIs

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

## Metadata design

## Representation design

## Client concerns

## Design principles