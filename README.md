# Consul net/rpc

**Important**: This fork was created exclusively for Consul. We will not accept
any changes to this repository for any other use cases. We discourage any use of this
library outside of Consul.


This repository contains a fork of the [net/rpc] package from the Go standard library.
The codec that Consul uses contains references to the `net/rpc` package, so this fork also contains
a fork of [hashicorp/net-rpc-msgpackrpc] and [hashicorp/go-msgpack] so those references
to `net/rpc` point at the correct package.

[net/rpc]: https://pkg.go.dev/net/rpc
[hashicorp/net-rpc-msgpackrpc]: https://github.com/hashicorp/net-rpc-msgpackrpc
[hashicorp/go-msgpack]: https://github.com/hashicorp/go-msgpack

## Why does this fork exist?

[net/rpc] has been [frozen since late 2016], which means no new changes will be made
to the package, and none of the existing bugs will be fixed.

Consul uses `net/rpc` for almost all of its many RPC endpoints. Consul has started to use
gRPC for some new RPC endpoints, but the work required to port all of the existing RPC
endpoints to gRPC will take multiple years. Even after all endpoints are converted to
gRPC, `net/rpc` endpoints will need to remain for at least one more major release to
support upgrades of existing Consul deployments.

Consul users face operational challenges that require additional features that `net/rpc`
does not provide. To address those problems we need a way of making changes to `net/rpc`.

This fork allows us to add some minor functionality to `net/rpc` while we work on porting
all existing RPC endpoints to gRPC.

[frozen since late 2016]: https://github.com/golang/go/issues/16844

## How is this fork different from the stdlib and go-msgpack?


We will limit the changes to this fork in the following ways:

* Any additions to `net/rpc` should be minimal hooks that allow functionality to be added
  from Consul. For example, an interceptor added to `Server`;
* Any deletes should be of functions that are not used by Consul;
* We will try to preserve the existing code structure as much as possible, for the unlikely
  case that we need to apply a patch from the stdlib;
* All changes should be additive, including existing test cases;
* `hashicorp/net-rpc-msgpackrpc` and `hashicorp/go-msgpack` should only receive updates
  from the upstream remote, with one exception. In some cases we may delete code that is
  not used by Consul, to allow the deletion of functions from this fork of `net/rpc`.
