# REST API design best practices


## Table of content

- [Objectives
](#objectives)

- [Introduction
](#introduction)

- [Designing URIs
](#designing-uris)
    - [URI format design
](#uri-format-design)
    - [URI authority desing
](#uri-authority-desing)
    - [Resources modeling
](#resources-modeling)
    - [URI path design
](#uri-path-design)
    - [URI query desing
](#uri-query-desing)

- [Interaction designs with HTTP
](#interaction-designs-with-http)

- [Metadata design
](#metadata-design)

- [Representation design
](#representation-design)
    - [Message body format desing
](#message-body-format-desing)

    - [Media type representation
](#media-type-representation)

    - [Error representation design
](#error-representation-design)

- [Client concerns
](#client-concerns)

- [Design principles
](#design-principles)

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

```
URI = scheme "://" authority "/" path [
    "?" query
] [
    "#" fragment
]
```

#### **Rule:** Forward slash separator (/) must be used to indicate a hierarchical relationship.

Example: 

```
http: //api.canvas.restapi.org/shapes/polygons/quadrilaterals/squares
```

#### **Rule:** A trailing forward slash (/) should not be included in URIs

As the last character within a URI’s path, a forward slash (/) adds no semantic value and may cause confusion. REST APIs should not expect a trailing slash and should not include them in the links that they provide to clients.

Many web components and frameworks will treat the following two URIs equally:

```
http: //api.canvas.restapi.org/shapes/
http: //api.canvas.restapi.org/shapes
```
However, every character within a URI counts toward a resource’s unique identity. Two different URIs map to two different resources.

#### **Rule:** Hyphens (-) should be used to improve the readability of URIs.

Example:
```
http: //api.example.restapi.org/blogs/mark-masse/entries/this-is-my-first-post
```

#### **Rule:** Underscores (_) should not be used in URIs.

Sometimes Underlining URIs, depending on the application’s font, the underscore (_) character can either get partially obscured or completely hidden. To avoid this confusion, use hyphens (-) instead of underscores as described above.

#### **Rule:** Lowercase letters should be preferred in URI paths.

RFC 3986 defines URIs as case-sensitive except for the scheme and host components. For example:

```
http: //api.example.restapi.org/my-folder/my-doc  
HTTP: //API.EXAMPLE.RESTAPI.ORG/my-folder/my-doc  
http: //api.example.restapi.org/My-Folder/my-doc
```

The First and the second URI are the same. The third URI is not the same as URIs 1 and 2, which may cause unnecessary confusion.

#### **Rule:** File extensions should not be included in URIs

URIs should rely on the media type, as communicated through the Content-Type header, to determine how to process the body’s content. For more about media types

```
http: //api.college.restapi.org/students/3248234/transcripts/2005/fall.json
```
File extensions should not be used to indicate format preference.

```
http: //api.college.restapi.org/students/3248234/transcripts/2005/fall 
```

REST API clients should be encouraged to utilize HTTP’s provided format selection mechanism, the Accept request header.

```
Accept:  application/json
```

Using media type negotiation clients can select a format.

### URI authority desing

#### Rule: Consistent subdomain names should be used for your client developer portal
 The full domain name of an API should add a subdomain named api. For example:

```
http: //api.soccer.restapi.org
```

#### Rule: Consistent subdomain names should be used for your client developer portal

If an API provides a developer portal, by convention it should have a subdomain labeled developer. For example:

```
http: //developer.soccer.restapi.org
```

### Resources modeling

#### Collection

The URI path conveys a REST API’s resource model, with each forward slash separated path segment corresponding to a unique resource within the model’s hierarchy. For example, this URI design:

```
http: //api.soccer.restapi.org/leagues/seattle/teams/trebuchet
```

Indicates that each of these URIs should also identify an addressable resource:

```
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

```
POST /alerts/245743/resend
```

### URI path design

Assigning meaningful values to each path segment helps to clearly communicate the hierarchical structure of a REST API’s resource model design.

#### Rule: A plural noun should be used for collection names

Example:

```
http: //api.soccer.restapi.org/leagues
```

#### Rule: A verb or verb phrase should be used for controller names

Like a computer program’s function, a URI identifying a controller resource should be named to indicate its action. For example:

```
http: //api.college.restapi.org/students/morgan/register
http: //api.example.restapi.org/lists/4324/dedupe
http: //api.ognom.restapi.org/dbs/reindex
```

#### Rule: Variable path segments may be substituted with identity-based values

The URI Template syntax allows designers to clearly name both the static and variable segments. A URI template includes variables that must be substituted before resolution.  The URI template example below has three variables (leagueId, teamId, and playerId):

```
http: //api.soccer.restapi.org/leagues/{leagueId}/teams/{teamId}/players/{playerId}
```


#### Rule: CRUD function names should not be used in URIs

HTTP request methods should be used to indicate which CRUD function is performed.

Example:

```
DELETE /users/1234
```

The following are antipatterns:

```
GET /deleteUser?id=1234
GET /deleteUser/1234
DELETE /deleteUser/1234
POST /users/1234/delete
```

### URI query desing

Optional **query** comes after the path and before the optional fragment:

```
URI = scheme "://" authority "/" path [
    "?" query
] [
    "#" fragment
]
```

#### Rule: The query component of a URI may be used to filter collections or stores

In the next example, the response message’s state representation contains a listing of all the users in the collection:

```
GET /users
```

In the next example, the response message’s state representation contains a filtered list of all the users in the collection with a “role” value of admin:

```
GET /users?role=admin
```

#### Rule: The query component of a URI should be used to paginate collection or store results

A REST API client should use the query component to paginate collection and store results with the pageSize and pageStartIndex parameters. The pageSize parameter specifies the maximum number of contained elements to return in the response. The pageStartIndex parameter specifies the zero-based index of the first element to return in the response. For example:

```
GET /users?pageSize=25&pageStartIndex=50
```

We can consider designing a special controller when the complexity of a client's pagination or filtering exceeds  the simple formating capabilities of the quuery part.

Example of a special controller:

```
POST /users/search
```

## Interaction designs with HTTP

EN DESARROLLO

## Metadata design

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

```
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

```
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

```
{
    "firstName": "John"
}
```

**Number**: Numbers in JSON must be an integer or a floating point.

exaples:

```
{
    "age": 15,
    "weight": 15.55
}
```

**Object**: Values in JSON can be objects.

Example:

```
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

```
{
    "employees": ["Anna", "John"]
}
```

**Boolean**: Values in JSON can be boolean {true | false}

Example:

```
{
    "onSale": true
}
```

**null**: Values in JSON can be null.

Example:

```
{
    "midleName": null
}
```



### Error representation design

Http's 400 and 500 error status codes should be used with clien readable information in the response message’s entity body.

#### Rule: A consistent form should be used to represent errors

This rule describes the reprsentation design for a single error.

The example below show the representation error desing using the Content-type: application/json:

```
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

```
{
    "elements": [
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

## Client concerns

## Design principles
