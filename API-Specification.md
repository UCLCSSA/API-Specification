# UCLCSSA API v1 Specification

## Introduction

This specification documents the UCLSSA API v1 for interoperability between
clients and services. The specification builds atop of:

- HTTP/1.1 network protocol: [RFC2616](https://tools.ietf.org/html/rfc2616)
- API style: [REST](https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)
- Data interchange format: [JSON](https://www.json.org/)
- Client-Server communication protocol: [HTTP/1.1 @ RFC7230](https://tools.ietf.org/html/rfc7230)
- Payload compression: [gzip](https://tools.ietf.org/html/rfc1952) and [Compression Codings @ RFC7230§4.2](https://tools.ietf.org/html/rfc7230)

Relevant REST information may be found at [Standards.REST](http://standards.rest/).

This API specification is modeled after [GitHub API v3](https://developer.github.com/v3/).

## Conventions Used in this Specification

The requirement level keywords "MUST", "MUST NOT", "REQUIRED", "SHALL",
"SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" used
in this specification are intended to be interpreted according to [RFC2119](https://www.ietf.org/rfc/rfc2119.txt).

Relevant terminology from the `HTTP/1.1` (`RFC2616`) standard is used where
appropriate, such as `header`, `body`.

## Current API Version

By default, all requests to `https://api.uclcssa.cn` SHALL dispatch to the `v1`
REST API. All requests SHOULD explicitly include the `Accept` HTTP header for
stability against API changes.

```
Accept: application/vnd.uclcssa.v1+json
```

For more details, refer to [Media Types](#media-types).

## Schema

All API communication MUST occur over HTTPS, at `https://api.uclcssa.cn`. The
data interchange format used MUST be JSON.

For example, the response to a request to `https://api.uclcssa.cn/users/testuser`
fetching headers should return

```http
curl -i https://api.uclccsa.cn/users/testuser
HTTP/1.1 200 OK
Server: nginx
Date: Thu, 13 Jun 2019 16:50:11 GMT
Content-Type: application/json; charset=utf-8
Content-Encoding: gzip
Content-Length: 5
Connection: keep-alive
Status: 200 OK
ETag: "5eb63bbbe01eeed093cb22bb8f5acdc3"
Cache-Control: max-age=0, private, must-revalidate
X-Media-Type: uclcssa.v1
X-Content-Type-Options: nosniff
```

Suitable HTTP headers SHOULD be used to convey additional information, such as
authentication, caching, API versioning and access control. See [HTTP Headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers) for more information.

Notes:
- `Date` format SHOULD conform to `HTTP-date` in [RFC7231](https://tools.ietf.org/html/rfc7231)
- `Content-Encoding` SHOULD be specified to use `gzip`.
- `X-*` headers are custom headers for additional metadata.
- `X-Media-Type: uclcssa.v1` specifies the API version as `v1`.
- There are no rate limits yet - restricting traffic flow, e.g. for
load-balancing is not yet considered in `v1` API.
- `ETag` header contains a validation token to facilitate resource caching.
Should the request's `ETag` token match the desired resource of the server,
then no payload need to be returned. The server SHOULD return a `304 Not
Modified` status code.
- `Content-Length` specifies the size of the body in bytes.

Blank fields MUST be included with value `null` instead of being omitted.

All timestamps MUST be returned in [RFC3339](https://tools.ietf.org/html/rfc3339)
format.

## Summary Representations

When requesting a *list* of certain resources, only their *summary
representations* are returned for performance reasons.

For example, when trying to fetch a list of nearby restaurants, only the summary
representations of the restaurants are returned.

## Detailed Representations

When requesting an individual resource, such as a `library`, all of its
attributes and detailed information are returned, depending on the requester's
permissions.

## Authentication

All authentication for restricting access to server resources MUST be handled
via [OAuth 2.0](https://oauth.net/2/) mechanisms (hereafter abbreviated as
`OAuth2`), standardized by
[RFC6749](http://tools.ietf.org/html/rfc6749).

Tutorials for `OAuth2` can be found at [bubblecode](http://www.bubblecode.net/en/2016/01/22/understanding-oauth2/).

When clients successfully register with the authentication server via
`UCL email address` and `WeChat id`, they are granted specific OAuth2 `tokens`.

Upon requesting any access-restricted resource, as such personal timetables,
the client MUST send their OAuth2 `token` via the `Authorization` http header.

For example,

```http
curl -H "Authorization: token {OAUTH-TOKEN}" https://api.uclcssa.cn
```

### Failed Login Limit

Upon receiving invalid credentials, the server MUST return `401 Unauthorized`
status code to notify the client.

Upon 5 requests trying to authenticate via invalid credentials, the server
MUST reject any authentication requests from the client for 10 minutes,
beginning from the fifth failed attempt, with the status code `403 Forbidden`.

## Parameters

For `GET` requests, parameters SHOULD be passed as `HTTP query string parameter`,
with the format `https://api.uclcssa.cn/resource?{key1}={value1}&{key2}={value2}`.

That is, query string parameters are suffixes to the URI of the resource,
beginning with query operator `?`, then followed by key-value pairs, with
multiple pairs conjuncted with the `&` operator.

Additional data SHALL be sent as `JSON` body (i.e. `Content`).

## Client Errors

If clients send requests with invalid JSON body, that is, if clients send:

1. **Invalid JSON**: The server MUST return with `400 Bad Request` response.

```http
HTTP/1.1 400 Bad Request
Content-Length: 31

{"message":"Cannot parse JSON"}
```

2. **Incorrect JSON value types**: The server MUST return with `400 Bad Request`
response.

```http
HTTP/1.1 400 Bad Request
Content-Length: 40

{"message":"Incorrect JSON value types"}
```

3. **Invalid fields**: The server MUST return with `422 Unprocessable Entity`
response.

```http
HTTP/1.1 422 Unprocessable Entity
Content-Length: 149

{
  "message": "Validation Failed",
  "errors": [
    {
      "resource": "Library",
      "field": "title",
      "code": "missing-field"
    }
  ]
}
```

Each `error` object in `errors` SHOULD contain `resource`, `field` and `code`
entries for diagnostics.

Possible validation error codes include:

| Error Code        | Description                                   |
| ----------------- | --------------------------------------------- |
| `missing`         | Resource does not exist.                      |
| `missing-field`   | Required field missing.                       |
| `invalid`         | Incorrect formatting.                         |
| `duplicate`       | Resource with identical value already exists. |

Resources MAY also respond with `custom` error codes, which means they MUST
then have a complementary `message` field for describing the problem.

For example,

```http
HTTP/1.1 422 Unprocessable Entity
Content-Length: 191

{
  "message": "Validation Failed",
  "errors": [
    {
      "resource": "Library",
      "field": "title",
      "code": "custom",
      "message": "UCL API service unavailable"
    }
  ]
}
```
