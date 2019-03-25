# Overview
Implementation of a simple client for Redis and demonstration of a distributed lock based on Redlock.

## Disclaimer
This project was created for a distributed systems class and is therefore *not production-ready*.

## Components
### RESP-Client (resp_client.h)
Provides an implementation for the RESP-protocol as well as member-functions to facilitate Redis pipeline mechanism and Pub / Sub.

Uses the RESP-Parser (`resp_parser.h`) to parse RESP-responses and returns one of the wrapper-types defined in `resp_types.h`.

### Redis-Client
Provides a high-level abstraction for Redis commands and tries to provide an idiomatic interface. Internally uses a RESP-Client.


## Usage
### Establishing a connection
To establish a connection to a running Redis instance simply instanciate a `redis_client` and pass a `shared_ptr` to a `resp_client` into the constructor.

```cpp
  redis_client client{make_shared<resp_client>(host, port)};
```

### Working with the client
The `redis_client`-class provides a variety member-functions that allow you to interact with a Redis instance.

```cpp
  client.set("my-key", "my-value");
  auto value = client.get("my-key");
  auto was_set = client.setnx("my-key", "set by SETNX");

  // set expiration time
  client.expire("my-key", 10);
  auto ttl = client.ttl("mein_key");

  client.lpush("values", {
    "1", "2", "3"
  });

  auto values = client.lrange("values", 1, 2);

  // send pipelined commands
  client.send_pipelined({
    resp_types::command({"set", "a", "a"}),
    resp_types::command({"set", "mein_value", "pipelined"})
  });

```

#### Working with Pub / Sub
The `redis_client` also provides a way to deal with subscriptions. To subscribe to a particular channel, simply call the subscribe member-function. 
```cpp
  subscriber channel_six_sub = client.subscribe("channel-six", 
    [&](resp_types::subscription_message message) -> auto {
      std::cout << "Channel Six Message: " << message.message << std::endl;
  });

```
This will internally create a new connection to the Redis instance and return a `subscriber`. This acts like an asynchronous function call. If you need the statement to act as a synchronous call, use the `subscribe_sync` member-function.
