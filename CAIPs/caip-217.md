---
caip: 217
title: Authorization Scopes
author: Pedro Gomes (@pedrouid), Hassan Malik (@hmalik88), Juan Caballero (@bumblefudge)
discussions-to: ["https://github.com/ChainAgnostic/CAIPs/discussions/217","https://github.com/ChainAgnostic/CAIPs/discussions/211"]
status: Draft
type: Standard
created: 2022-11-09
---

## Simple Summary

This CAIP defines a simple syntax for scopes of authorization between
applications (e.g. dapps) and user-agents (e.g. "wallets" or signers). These are
expressed as JSON objects as a building block across multiple protocols and
mechanisms, for example:
- A JSON-RPC protocol for persisting and synchronizing authorized sessions
  ([CAIP-25][])
- Routing individual RPC commands to an authorized network ([CAIP-27][])

## Motivation

The layering of today's cryptographic and decentralized systems favors
loosely-coupled combinations of protocols (representated in the CAIPs model as
[namespaces][]), instances or consensus-communities within those protocols
(addressed in the CAIPs model as [CAIP-2][] URNs), and sets of supported RPC
methods and notifications used in those namespaces. Bundling all of these into
an object facilitates unambiguous authorization schemes, including progressive
authorization patterns, feature discovery, authority negotiation (See
[CAIP-211][]) and delegations.

## Specification

An authorization scope is represented in JSON as an object which is keyed to a
string that expresses its target network and contains arrays of strings
expressing the various capabilities authorized there. When embedded in any other
JSON context (including the `params` of a JSON-RPC message), the object MUST be
expressed as the value of a property named by the scope string. 

### Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" written in
uppercase in this document are to be interpreted as described in [RFC
2119](https://www.ietf.org/rfc/rfc2119.txt)

### Definition

The syntax of a `scopeObject` is as follows:

```jsonc


[scopeString]: {
   *scopes: [(chainId)+],
   methods: [(method_name)+],
   notifications: [(notification_name)+],
   *accounts: [(account_id)+]
   *rpcDocuments: [(rpcDocument)+],
   *rpcEndpoints: [(rpcEndpoint)+]
}
```

Where:

- {`scopeString`} (conditional) = EITHER a namespace identifier string registered in the CASA [namespaces][] registry to authorize multiple chains with identical properties OR a single, valid [CAIP-2][] identifier, i.e., a specific `chain_id` within a namespace.
- `scopes` (conditional) = An array of 0 or more [CAIP-2][] `chainId`s. For each
  entry in `scopes`, all the other properties of the `scopeObject` apply, but in
  some cases, such as when members of `accounts` are specific to 1 or more
  chains in `scopes`, they may be ignored or filtered where inapplicable;
  namespace-specific rules for organizing or interpreting properties in
  multi-scope MAY be specified in a [namespace-specific profile][namespaces] of
  this specification.
  - This property MUST NOT be present if the object is already scoped to a single `chainId` in the string value above.
  - This property MUST NOT be present if the scope is an entire [namespace][namespaces] in which `chainId`s are not defined.
  - This property MAY be present if the scope is an entire [namespace][namespaces] in which `chainId`s are defined.
- `methods` = An array of 0 or more JSON-RPC methods that an application can call on the agent and/or an agent can call on an application.
- `notifications` = An array of 0 or more JSON-RPC notifications that an application send to or expect from the agent.
- `accounts` (optional) = An array of 0 or more [CAIP-10][] identifiers, each valid within the scope of authorization.
- `rpcDocuments` (optional) = An array of URIs that each dereference to an RPC document specifying methods and notifications applicable in this scope. 
  - These are ordered from most authoritative to least, i.e. methods defined more than once by the union of entries should be defined by their earliest definition only.
- `rpcEndpoints` (optional) = An array of URLs that each dereference to an RPC endpoints for routing requests within this scope. 
  - These are ordered from most authoritative to least, i.e. priority SHOULD be given to endpoints in the order given, as per the CAIP-211 profile for that [namespace][namespaces], if one has been specified.

Additional constraints MAY be imposed by the usage of `scopeObject`s in
protocols such as [CAIP-25][], and specific [namespaces][] may have
implicit values or validity constraints for these properties.

Whenever another CAIP uses the name `scopeObject` and has this CAIP in the
`required` front-matter property, it SHALL be interpreted as reference to this
specification.

## References

[CAIP-10]: https://chainAgnostic.org/CAIPs/CAIP-10
[CAIP-25]: https://chainAgnostic.org/CAIPs/CAIP-25
[CAIP-27]: https://chainAgnostic.org/CAIPs/CAIP-27
[CAIP-211]: https://chainAgnostic.org/CAIPs/CAIP-211
[namespaces]: https://namespaces.chainAgnostic.org/

## Copyright

Copyright and related rights waived via
[CC0](https://creativecommons.org/publicdomain/zero/1.0/).