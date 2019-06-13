# UCLCSSA API v1 Specification

## Introduction

This specification documents the UCLSSA API v1 for interoperability between
clients and services. The specification builds atop of:

- API style: [REST](https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)
- Data interchange format: [JSON](https://www.json.org/)
- Client-Server communication protocol: [HTTP/1.1 @ RFC7230](https://tools.ietf.org/html/rfc7230)
- Payload compression: [gzip](https://tools.ietf.org/html/rfc1952) and [Compression Codings @ RFC7230§4.2](https://tools.ietf.org/html/rfc7230)

Relevant REST information may be found at [Standards.REST](http://standards.rest/).

## Conventions Used in this Specification

The requirement level keywords "MUST", "MUST NOT", "REQUIRED", "SHALL", 
"SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" used
in this specification are intended to be interpreted according to [RFC2119](https://www.ietf.org/rfc/rfc2119.txt).
