---
layout: default
title: HIP Template
parent: Hyperledger Improvement Proposals
---

# HIP Identifier
Hyperledger YUI

# Sponsor(s)
* Jun Kimura, CTO, Datachain, Inc. - https://github.com/bluele
* Ryo Sato, Datachain, Inc. https://github.com/3100
* Masanori Yoshida, Datachain, Inc. - https://github.com/siburu
* Jeffrey Hu, director of research, Bianjie AI - https://github.com/HuLaTown
* Shinichi Yamashita, NTT DATA - Shinichi.Yamashita@jp.nttdata.com

# Abstract
The Hyperledger YUI is an interoperability project to achieve trustless application-agnostic exchange of information for heterogeneous ledgers both enterprise permissioned and public blockchains.

# Context
*Review of Hyperledger Labs (WIP)*

# Dependent Projects
*(WIP)*
- IBC from Cosmos
- Hyperledger Fabric
- Hyperledger Besu

# Motivation
*(WIP:Key sentences)*

As the commercialization of products and solutions based on blockchains progresses, there is a growing need for interoperability solutions to connect multiple blockchain systems.

A straightforward way to bring interoperability would be to exchange data via a certain authority or a trusted third party (TTP). Traditional API between blockchain systems fall into this category in terms of trusting the endpoint provider of API. On the other hand, there are issues with TTP, thus solutions that do not rely on a TTP are needed.

One of the issues is that the TTP (in the API example, a provider or an administrator of API endpoint) must be trusted by all participants, which requires cost for trust, and sometimes impractical assumption to maintain, especially in a network with frequent change in participants.

In addition, the complexity of the entire system is increased because it requires a mechanism for information exchange which typically involves off-chain modules, such as API. This requires storing and maintaining intermediate states on off-chain, resulting in high cost for achieving availability and consistency.

There exist similar solutions to the TTP, that is to connect multiple blockchains to a single system. (This is sometimes referred to as an “integrated” solution.) However, such a system has the challenges of TTP and the cost of requiring developers to understand and operate multiple layers (“integrated” and “individual” layers).


# Status
Proposal

# Solution
*(WIP:Key sentences)*

Based on the above motivation, we propose the Hyperledger YUI, which aims to satisfy the following properties. (This also structures design principles)
1. Provide a unified communication method independent from a specific blockchain/DLT implementation
    * This enables application developers to develop chain-agnostic application logics.
2. Support arbitrary data transfer and computation
    * Developers can design and implement the specific protocol for its cross-chain application, not limited to token transfer use cases.
3. Cross-authentication
    * By relying on the on-chain verification of the other chain’s state, cross-chain communication can be achieved without any safety concern due to the behavior of off-chain actors
    * This allows cross-chain message relaying not only to the trusted and privileged actors, but also to any actor that satisfies the ACL of the permissioned chain.
    * In addition, we aim to apply the method not only to the permissioned chains, but to the public chains as well.
4. Avoid adding components that introduce additional trust (apart from individual blockchains)
    * Introducing such components may decrease the level of security of a system as it would be bounded by the lowest component in the system.
    * Also, introducing new (off-chain) components with high fault tolerance will also lead to increased operational costs
5. Do not introduce a new layer (such as integration layer)
    * Each actor (of ledgers) has only to interact with a respective ledger of its interest to complete a cross-chain operation unless the application has additional requirements. 
    * In order to achieve interoperability that satisfies design principles above, YUI focuses on inter-blockchain communication protocol (IBC) designed as a core component of the Cosmos network and applies it to the enterprise blockchain domain to achieve communication between blockchains.

IBC enables communication among heterogeneous blockchain with TAO property, namely, (reliable) transport, authentication, and ordering. 

* “Reliable” means that only a packet that is exactly sent by a source chain is received by a destination chain exactly once. In fact, blockchains themselves never communicate with other blockchains. Thus a “relayer” needs to relay packets from one to another, but the role of relayer is permissionless; that is, anyone can run relayers.
* “Authenticated” means that IBC uses “channel” abstraction described later when relaying packets and that each end of a channel is exclusively assigned to a specific smart contract. Thus, if a packet is received by a destination chain via a channel, it proves that a specific smart contract assigned to the channel on a source chain sent the packet and that any other smart contracts can never use the channel to send packets.
* “Ordered” means that a destination chain receives packets in the same order in which a source chain sends the packets.

Modules for IBC communication are implemented as smart contracts that operate on both blockchains and consist of the following components:
* On-chain light client
* Connection abstraction
* Channel abstraction

Fig1: Architecture with IBC modules
![architecture](images/yui/yui_IBC_architecture.svg)

The on-chain light client is a basis for IBC/TAO, which verifies that presented states actually exist on an opposite blockchain without relying on any trusted third parties. On top of that, connection abstraction and channel abstract are defined and used to connect two smart contracts on two blockchains and to relay packets between them.

IBC requires Commitment Proof which can be verified by the on-chain light client to check if a certain state (a key value pair) exists (or does not exist) in the target blockchain. For this reason, the specification of commitment proof for each blockchain should be defined.

For example in YUI, YUI-Fabric-IBC is a collection of modules for Hyperledger Fabric. We designed and implemented Endorsed Commitment, a design for commitment proof for Hyperledger Fabric.

In Hyperledger Fabric, basically state updates and corresponding blocks are signed by endorser(s) to be accepted. 

Thus in YUI-Fabric-IBC, read/write set of a certain key/value pair signed by the appropriate endorser(s) can formulate a proof of the state (called an Endorsed Commitment). The on-chain light client can verify these signatures provided endorsement policy of target blockchain.

We can develop several cross-chain applications on top of YUI, examples are:
* Cross-chain token transfer
* Cross-chain Atomic swap
  * Example includes delivery versus payment settlement
* Cross-chain atomic execution of arbitrary procedures


# Effort and Resources
(WIP)

# How To
(WIP)
Refer to yui-docs repository

# References
(WIP)

# FAQs


# Closure
The goal of YUI is to realize interoperability and applications with cross-chain functionalities in the enterprise domain. The success of the project can be measured by the number of projects that are built on top of it.

