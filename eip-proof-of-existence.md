---
eip: <to be assigned>
title: Proof of Existence
author: David Ammouial (@davux) <david.ammouial@nttdata.com>
discussions-to: <URL>
status: Draft
type: Standards Track
category: ERC
created: 2019-04-04
---

## Simple Summary
This EIP defines a generic interface to prove/verify that a given document existed at a certain point in time, by recording its hash in an Ethereum blockchain.

## Abstract
It is often necessary to prove that a certain content already existed at some point. Use cases are varied and include intellectual property or insurance claims. As a secondary feature, it is often desirable to easily tell *who* claimed a given content first, although it is always possible to include that information in the document itself.

The approach is to provide a standard interface to associate a timestamp and author's address to a given hash. Additionally, a standard event name is defined, which allows any observer to search for a given hash without having to trust the logic of a specific contract.

## Motivation
Project often face the need to prove that a specific content existed. In fact that is one of the most obvious uses for a blockchain. Unfortunately, although the goal is shared and simple, there is no standard interface to achieve that simple task, which leads to many similar yet incompatible initiatives. This specification aims to unify those efforts.

## Specification

### Use Cases

This specification tries to solve the following use cases:

Proving:
- (P1) A user wants to record the fact that a certain content exists as of now
- (P2) A user wants to record from a particular address the fact that a certain content exists as of now

Verifying:
- (V1) A user wants to check whether a certain content already existed before now
- (V2) A user wants to check the earliest date a certain content was proven to exist
- (V3) A user wants to check the first address to prove that a certain content existed

### Approaches

This EIP defines two simultaneous approaches:
- Using a smart contract's state and logic to store and record hashes. This assumes trust to the smart contract's bytecode.
- Using events to store and record hashes. This approach is less persistent, as events are not supposed to be use to store information, but it removes the need to trust a particular contract's logic as it relies on transaction-level features of Ethereum.

### Interface

#### Events

The following event is defined:

```
event Stamped(bytes32 indexed documentHash);
```

This will write the hash into the transaction's log. The advantage of this approach is that no matter the logic of the contract that emitted an event with that signature, the mere existence of a givent document's hash in a transaction log is sufficient to prove that that document existed before at the time of the transaction or earlier.

Conceptually, any event signature will achieve the same goal, but this EIP defines a specific signature to make filtering easier and facilitate interoperable implementations.

#### Functions

To complement the event approach, the following function is defined to create a new timestamp:

```
  stamp(bytes32 documentHash) public
```

The following function may be used to retrieve a given hash:

```
  records(bytes32 documentHash) public view returns (uint timestamp, address author)
```

* `timestamp` is the earliest timestamp the given hash was recorded at in that contract
* `author` is the first address to ever record the given hash in that contract



## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

This specification relies on hashes rather than explicit data. This is a common practice that has the following purposes:
- It is equivalent to prove the existence of a content or the existence of its hash, because of fundamental properties of hashes.
- Storing a hash doesn't give away any private information that a reader doesn't already have.
- Hashes have a fixed size of 32 bytes, whether content have a variable (often much larger) size, meaning hashes are cheaper to store and manipulate.

## Limitations and possible workarounds

* Sometimes, a user wants to store meta-data in addition to a document.
  * A possible workaround is to build a meta-document around the original document with all the necessary information, and to hash that meta-document.
  * Some implementations prefer to store a "meta" field next to the hash, but we found it simpler and funcionally equivalent to just hash one piece of information.
* Sometimes, a user wants to hide the fact that a certain document was recorded, even to some of the people who already know that document.
  * A possible workaround is to add a secret salt to the document, and to provide the salt (and the salting algorithm: simple append, etc.) in addition to the document at proving time.

## Backwards Compatibility
<!--All EIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The EIP must explain how the author proposes to deal with these incompatibilities. EIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
All EIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The EIP must explain how the author proposes to deal with these incompatibilities. EIP submissions without a sufficient backwards compatibility treatise may be rejected outright.

## Test Cases
<!--Test cases for an implementation are mandatory for EIPs that are affecting consensus changes. Other EIPs can choose to include links to test cases if applicable.-->
Test cases for an implementation are mandatory for EIPs that are affecting consensus changes. Other EIPs can choose to include links to test cases if applicable.

## Implementation
<!--The implementations must be completed before any EIP is given status "Final", but it need not be completed before the EIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
The implementations must be completed before any EIP is given status "Final", but it need not be completed before the EIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).

