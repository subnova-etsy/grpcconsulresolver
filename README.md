# Consul Resolver for the grpc-go library

The repository provides a consul resolver for the
[GRPC-Go Library](https://github.com/grpc/grpc-go) >= 1.26.

To register the resolver with the GRPC-Go library run:

```go
resolver.Register(consul.NewBuilder())
```

Afterwards it can be used by calling grpc.Dial() and passing an URI in the
following format:

```
consul://[<consul-server>]/<serviceName>[?<OPT>[&<OPT>]
```

`<OPT>` is one of:

- `scheme=(http|https)` -   defines if the connection to consul is
                            established via http or https
- `tags=<tag>[,<tag>]...` - specifies that the consul service entry must have
                            one of those tags

The default values for the optional parts are:

- `consul-server`: http://127.0.0.1:8500
- `scheme`:        http
- `tags`: none

## Example

```go
package main

import (
  "google.golang.org/grpc"
  "google.golang.org/grpc/resolver"

  "github.com/simplesurance/grpcconsulresolver/consul"
)

func init() {
  // Register the consul consul at the grpc-go library
  resolver.Register(consul.NewBuilder())
}

func main() {
  // Create a GRPC-Client connection with the default load-balancer.
  // The addresses of the service "user-service" with the tags
  // "primary" or "eu" are resolved via the consul server "10.10.0.1:1234".
  client, _ := grpc.Dial("consul://10.10.0.1:1234/user-service?scheme=https&tags=primary,eu")

  // Instantiates a GRPC client with a round-robin load-balancer.
  // The addresses of the service "metrics" are resolved via the default
  // consul server "http://127.0.01:8500".
  client, _ = grpc.Dial("consul://metrics", grpc.WithBalancerName("round_robin"))
}
```
