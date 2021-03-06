# Wallet Connect v.2 - Swift
Swift implementation of WalletConnect v.2 protocol for native iOS applications.
## Requirements 
- iOS 13
- XCode 13
- Swift 5

## Documentation
- In order to build API documentation in XCode go to Product -> Build Documentation
- [Getting started with wallet integration](https://docs.walletconnect.com/2.0/quick-start/wallets/swift)
- [Beginner guide to WalletConnect v2.0 for iOS Developers](https://medium.com/walletconnect/beginner-guide-to-walletconnect-v2-0-for-swift-developers-4534b0975218)
- [Protocol Documentation](https://docs.walletconnect.com/2.0/protocol/client-communication)
- [Glossary](https://docs.walletconnect.com/2.0/protocol/glossary)

## Usage
### Responder
Responder client is usually a wallet.
##### Instantiate a Client
You usually want to have a single instance of a client in you app.
```Swift
        let metadata = AppMetadata(name: String?,
                                   description: String?,
                                   url: String?,
                                   icons: [String]?)
        let client = WalletConnectClient(metadata: AppMetadata,
                            projectId: String,
                            relayHost: String)
```

After instantiation of a client set its delegate.
#### Pair Clients
Pair client with a uri generated by the `proposer` client.
```Swift
let uri = "wc:..."
try! client.pair(uri: uri)
```
#### Approve Session
Sessions are always proposed by the `Proposer` client so `Responder` client needs either reject or approve a session proposal.
```Swift
class ClientDelegate: WalletConnectClientDelegate {
...
    func didReceive(sessionProposal: Session.Proposal) {
        client.approve(proposal: proposal, accounts: [String])
    }
...
```
or 
```Swift
    func didReceive(sessionProposal: Session.Proposal) {
        client.reject(proposal: proposal, reason: Reason)
    }
```
NOTE: addresses provided in `accounts` array should follow [CAPI10](https://github.com/ChainAgnostic/CAIPs/blob/master/CAIPs/caip-10.md) semantics.
#### Handle Delegate methods
```Swift
    func didReceive(sessionProposal: Session.Proposal) {
        // handle session proposal
    }
    func didReceive(sessionRequest: Request) {
        // handle session request
    }
```
#### JSON-RPC Payloads
#### Receive
You can parse JSON-RPC Requests received from "Requester" in `didReceive(sessionRequest: Request)` delegate function.

Request parameters can be type casted based on request method as below:
```Swift
            let params = try! sessionRequest.request.params.get([EthSendTransaction].self)
```
##### Respond

```Swift
            let jsonrpcResponse = JSONRPCResponse<AnyCodable>(id: request.id, result: AnyCodable(responseParams))
            client.respond(topic: sessionRequest.topic, response: .response(jsonrpcResponse))
```

## Installation
### Swift Package Manager
Add .package(url:_:) to your Package.swift:
```Swift
dependencies: [
    .package(url: "https://github.com/WalletConnect/WalletConnectSwiftV2", .branch("main")),
],
```
## Example App
open `Example/ExampleApp.xcodeproj`

## License

Apache 2.0

