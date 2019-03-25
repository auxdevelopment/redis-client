# Overview
Implementation of a simple client for Redis and demonstration of a distributed lock based on Redlock.

## Components
### RESP-Client (resp_client.h)
Provides an implementation for the RESP-protocol as well as member-functions to facilitate Redis pipeline mechanism and Pub / Sub.

Uses the RESP-Parser (`resp_parser.h`) to parse RESP-responses and returns one of the wrapper-types defined in `resp_types.h`.

### Redis-Client
Provides a high-level abstraction for Redis commands and tries to provide an idiomatic interface. Internally uses a RESP-Client.


## Usage
### Establishing a connection

C++```
  connection client{make_shared<resp_client>(host, port)};
```
