---
title: Staking FAQ
sidebar_title: FAQs
description: Frequently Asked Questions
---

### Where will users receive their staking reward for each staking option?

Staking rewards are not deposited directly into a user's account.
They are deposited to the user's rewards pool in their connected staking object
and can be withdrawn or restaked at any time.

If you staked using Flow Port, then you should be able to see your staking rewards there.
You can also withdraw the rewards or manually re-stake them through Flow Port.

If you staked using a staking provider such as Kraken, Blocto or Finoa,
please read [our guides](../staking/custody-providers.md) for more information.

### Will staking rewards be automatically re-staked?

There will be _no_ automatic re-staking of staking rewards with Flow Port (i.e. using Ledger or Blocto).
If you want to re-stake your rewards, you must manually do so yourself.

If you staked using a staking provider such as Kraken, Blocto or Finoa,
please read [our guides](../staking/custody-providers.md) for more information.

DeFi liquid staking strategies such as offered by [incrementFi](https://app.increment.fi/staking)
are not managed by the protocol or nodes, but are more sophisticated ways
to manage your staking.

### Does it make a difference as to what TYPE of node we delegate to in terms of rewards?

No, rewards are calculated the same for every node type.

### Can a validator change its fees?

The network enforces a delegation fee of 8% which cannot be directly changed.
Any different fees that nodes claim they have are rebates that they
offer using their own methods and are not enforced by the protocol. 

### Can a token holder stake to multiple nodes? If yes, how is the stake split between them?

A token holder can delegate to multiple nodes from a single account if they use the 
[Staking Collection](../staking/staking-collection.mdx).

The staking collection is enabled by default on Flow port, and many custody providers also support it.

### Is there a utility to generate the FLOW addresses?

Staking of FLOW has to be done via Flow port which requires a login through Ledger or Blocto.
Hence, a utility cannot be used to generate a Flow address since it cannot be used to stake via Flow port.

### Is the wallet key transferred to the staked node?

No - The keys on the node are different from the wallet keys. The wallet keys always stay in the wallet.
A node operator generates the staking and networking keys separately which will be used on the node.

### Can I stake through multiple accounts to meet the minimum FLOW required for staking a node?

No, the minimum stake must come from a single account for all node types.
Temporarily, the minimum for consensus nodes can come from a combination
of staking actions from two accounts controlled by the same party.

### How can I reach the Consensus node minimum stake of 500K FLOW

The consensus node minimum of 500K FLOW can be met with a minimum
250,000 FLOW staking action and additional delegation
adding a minimum of 250,000 more FLOW from the same entity.

### Is rewards payout another spork?

No, rewards payout is not a spork but (currently) a separate transaction
that the Flow governance committee initiates.

### Can I query an account address of a node ID or delegator ID?

The staking smart contract does not directly associate a node or delegator with an account address. 
It associates it with the assigned resource object that corresponds to that entry in the contract. 
There can be any number of these objects stored in the same account, 
and they can be moved to different accounts if the owner chooses.
It is possible to query the information about a node that an address runs though, by using the
`get_node_info_from_address.cdc` script.