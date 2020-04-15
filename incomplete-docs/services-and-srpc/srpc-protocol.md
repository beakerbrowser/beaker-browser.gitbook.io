---
description: >-
  The Simple Remote Procedure Call protocol uses WebSockets + JSON-RPC to
  interface with services.
---

# SRPC protocol

SRPC \(Simple Remote Procedure Calls\) is a wire protocol used by Beaker to integrate services into the browser. It is a stack of widely-accepted protocols along with processes for service description and API discovery.

SRPC uses:

* [WebSockets](https://en.wikipedia.org/wiki/WebSocket) over HTTPS as the default transport
* [HTTPS Basic Auth](https://en.wikipedia.org/wiki/Basic_access_authentication) as the default authentication scheme
* [JSON-RPC](https://www.jsonrpc.org/) as the wire-format

### Why WebSockets + JSON-RPC?

The WS+JSON-RPC stack is easy to build on the server. It requires minimal boilerplate to translate requests into function-calls \(unlike HTTP/REST\) and no a-priori exchange of schemas \(unlike [gRPC](https://grpc.io/)\). It can also be leveraged in Javascript clients without distributing a "client library" using only [Proxy Objects](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy).

{% code title="client.js" %}
```javascript
var service = await SRPC.connect('wss://foo.com', {test: 'example-api.com/test'})
console.log(await service.test.ping())
```
{% endcode %}

### API-discovery exchange

All SRPC services are expected to support the `describeSRPC()` method. If this method is not supported, the remote is not considered an SRPC provider.

Immediately after connecting, a client will call `describeSRPC()`. This method will return an object which matches the following form:

```javascript
{
    "title": "{service-title}", // eg "My SRPC Service"
    "apis": {
        "{api-name}": "{api-protocol}", // eg "test": "example-api.com/test"
        // ...
    }
}
```

The SRPC client will match the returned `apis` object against its own `apis` object and generate a mapping for all remote calls. If any of the desired APIs are not returned in the description, the client will emit a warning event but continue to function.

If the API-object supplied by the client and the server are equivalent, then no mapping will take place. For instance, given the following conditions:

```javascript
// client:
var service = await SRPC.connect('wss://foo.com', {test: 'example-api.com/test'})

// server `describeSRPC()` response:
{
    "title": "My SRPC Service",
    "apis": {
        "test": "example-api.com/test"
    }
}
```

...then the client-side call `service.test.ping()` will be sent to the server as `test.ping()`.

If the shortnames and protocol identifiers do not match, a corrected mapping will be created by the client. For instance, given the following conditions:

```javascript
// client:
var service = await SRPC.connect('wss://foo.com', {test: 'example-api.com/test'})

// server `describeSRPC()` response:
{
    "title": "My SRPC Service",
    "apis": {
        "exampleComTest": "example-api.com/test" // notice the different key
    }
}
```

...then the client-side call `service.test.ping()` will be sent to the server as `exampleComTest.ping()`.



