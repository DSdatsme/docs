---
title: Storing Data on Flow
sidebar_position: 4
---

## Overview

Each Flow account has an associated storage used. The account's storage used is the byte size of all the data stored in the account's storage. Accounts also have a storage capacity, which is directly tied to the amount of Flow tokens an account has. The account can, without any additional cost, use any amount of storage up to its storage capacity. **If a transaction puts an account over storage capacity, that transaction fails and is reverted. Likewise, if a transaction would drop an account's balance below 0.001 Flow tokens, which is the minimum an account can have, the transaction would also fail.**

## Storage Capacity

Storage capacity of an account is dictated by the amount of FLOW it has. **The minimum amount of FLOW an account can have is 0.001. This minimum is provided by the account creator at account creation.** The minimum account reservation ensures that most accounts won't run out of storage capacity if anyone deposits anything (like an NFT) to the account.

The exact amount of storage capacity an account has is the amount of FLOW the account has times the `storageBytesPerReservedFlow` variable defined on the `StorageFees` smart contract, which is:

- 100 MB per 1 reserved FLOW, or
- 10 MB per 0.1 reserved FLOW, or
- 1 MB per 0.01 reserved FLOW, or
- 100 kB per 0.001 reserved FLOW.

Any account can increase the storage capacity of an account by depositing more FLOW to the account.

If the accounts balance is below the minimum account balance, the account's storage capacity is considered to be 0.

### Storage Capacity of the Payer

The storage capacity of the Payer of a transaction is generally computed the same way as the capacity of any other account, however the system needs to account for the transaction fees the payer will incur at the end of the transaction. The final amount of transaction fees is not yet fully known at the step when accounts are being checked for storage compliance (if their storage used is below their storage capacity). Because of this, the payer's balance is conservatively considered to be lower by the maximum possible transaction fees, when checking for storage compliance. The maximum transaction fee of a specific transaction is the transaction fee as if the transaction would have used up all of its execution effort limit.

## Storage Used

All data that is in an account's storage counts towards storage used. Even when an account is newly created it is not empty. There are already some items in its storage:

- Metadata that marks that the account exists.
- An empty FLOW vault, and stored receiver capability.
- Public keys to the account if the account was created with keys.
- Smart contracts deployed on the account if the account was created with contracts.
- The value of the account’s storage used as an unsigned integer.

Adding additional keys, smart contracts, capabilities, resources, etc. to the account counts towards storage used.

Data stored on the Flow blockchain is stored in a key-value ledger. Each item’s key contains the address that owns the item and the path to the item. Just as the shipping cost of a box of things you send to a friend includes the weight of the carton box itself, storing items on flow takes into account the key it is stored with. This means that the storage used by each item is the byte length of the item plus the byte length of the item’s key.

## Storage Parameters

Two parameters define storage limits:

- Minimum account FLOW is **0.001 FLOW** and represents **100 kB** of storage capacity. This is also the amount of FLOW that the creator of a new account needs to provide for the account's storage reservation. (`StorageFees.minimumStorageReservation`)
- The amount of storage capacity one FLOW represents is **100 MB per FLOW**. (`StorageFees.storageMegaBytesPerReservedFlow`)

## FAQ

### Why is there an account minimum balance?

The more data that is stored on Flow, 
the higher the storage requirements for execution nodes that need to store the data. 
As the size of the data grows, 
it's more and more difficult and costly for the execution nodes to meet those requirements.
Storage fees were put in place to regulate the growth of data stored on the Flow blockchain.

### What is my current storage usage?

The values of storage used and storage capacity are available as fields both on `AuthAccount` and `PublicAccount` Cadence types.

```cadence
pub fun main(address: Address) {
    let account = getAccount(address)
    log(account.storageUsed)
    log(account.storageCapacity)
}
```

## Further reading

Relevant Cadence changes can be found [here](../../cadence/language/accounts.mdx#account-storage)

The **FLIP** (Flow Improvement Proposal) for storage fees can be found here: [20201310-storage-fees.md](https://github.com/onflow/flips/blob/main/flips/20201310-storage-fees.md).
