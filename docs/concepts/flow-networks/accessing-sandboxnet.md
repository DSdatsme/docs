---
title: Flow Sandboxnet
description: Guide to sandboxnet access
---

## Accessing Flow sandboxnet

The Flow sandboxnet is available for access at this URL:

```
access.sandboxnet.nodes.onflow.org:9000
```

For example, to access the network using the [Flow Go SDK](https://github.com/onflow/flow-go-sdk):

```go
import "github.com/onflow/flow-go-sdk/client"

func main() {
  flowAccessAddress := "access.sandboxnet.nodes.onflow.org:9000"
  flowClient, _ := client.New(flowAccessAddress, grpc.WithInsecure())
  // ...
}
```

### Generating sandboxnet key pair

You can generate a new key pair with the [Flow CLI](https://github.com/onflow/flow-cli) as follows:

```sh
> flow keys generate

🙏 If you want to create an account on sandboxnet with the generated keys use this link:
https://sandboxnet-faucet.flow.com/?key= cc1c3d72...


🔴️ Store private key safely and don't share with anyone!
Private Key      246256f3...
Public Key       cc1c3d72...
```

**Note: By default, this command generates an ECDSA key pair on the P-256 curve. Keep in mind, the CLI is intended for development purposes only and is not recommended for production use. Handling keys using a Key Management Service is the best practice.**

## Account creation and token funding requests

Accounts and tokens for testing can be obtained through the [sandboxnet faucet](https://sandboxnet-faucet.flow.com). If you generated the keypair through the CLI, you can click on the URL provided to create an account and request sandboxnet FLOW tokens.

## Important smart contract addresses

You can review [all available core contracts](../../cadence/core-contracts/) deployed to the sandboxnet to identify which ones you want to import.
