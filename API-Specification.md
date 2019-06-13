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

