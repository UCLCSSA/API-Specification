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
