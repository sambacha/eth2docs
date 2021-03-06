---
title: Transaction Object
description: Understanding the Transaction Object on Ethereum
id: concepts-txobj
---

# Understanding the Transaction Object on Ethereum

## Transaction Object

The transaction object on Ethereum is made up of the following components:

* `blockHash`: `DATA`, 32 Bytes - hash of the block where this transaction was in. null when its pending.
* `blockNumber`: `QUANTITY` - block number where this transaction was in. null when it's pending.
* `from`: `DATA`, 20 Bytes - address of the sender.
* `gas`: `QUANTITY` - gas provided by the sender.
* `gasPrice`: `QUANTITY` - gas price provided by the sender in Wei.
* `hash`: `DATA`, 32 Bytes - hash of the transaction.
* `input`: `DATA` - the data send along with the transaction.
* `nonce`: `QUANTITY` - the number of transactions made by the sender prior to this one.
* `to`: `DATA`, 20 Bytes - address of the receiver. null when it's a contract creation transaction.
* `transactionIndex`: `QUANTITY` - integer of the transactions index position in the block. null when its pending.
* `value`: `QUANTITY` - value transferred in Wei.
* `v`: `QUANTITY` - ECDSA recovery id
* `r`: `DATA`, 32 Bytes - ECDSA signature r
* `s`: `DATA`, 32 Bytes - ECDSA signature s

Below you will find more detailed explanations for each parameter on the transaction object.&#x20;

### `Hash`&#x20;

Every transaction that has been verified and added to the blockchain will receive an assigned `hash`. This creates a unique identifier for the transaction that can be used to locate and retrieve information about a specific transaction.

### `blockHash`&#x20;

Each block has a corresponding cryptographic hash which includes all the data of that block. The`blockHash` is the hash of the block where this transaction has been included. In the case of a pending transaction that has not been added to a block, you will receive a `null` value in return.

### `blockNumber`

&#x20;Once a block has been added to the blockchain, it is assigned the next available number in sequential order. Using the blockNumber, you can tell when the transaction has been added to a block.

### `from`&#x20;

Ethereum transactions have both a sender and receiver. The `from` field is the address of the sender and can be used to find out which address started the transaction.

### `gas`

For transactions to be completed and added to a block, the sender must provide an amount of gas in addition to the value of the transaction. Gas is used to cover the computation costs that are required to complete the transaction. This `gas` field shows you the amount of gas that was provided by the sender.

### `gasPrice`&#x20;

Another way to show the gas price is in wei. Wei is the smallest denomination of ETH and is a better way to work with smaller transactions of ETH. 1 wei is the equivalent of 10^-18 ETH. This `gasPrice` shows the amount of gas provided by the sender in WEI.

### `input`&#x20;

Data can be added to the data field of a transaction whenever a user deploys or makes a call to a function in a smart contract. This data is a message to the smart contract that points to the function that will be executed.

The `input` field contains the data that is included in the transaction. If this field is empty, the transaction is a transfer between users and doesn't involve a smart contract.

### `nonce`&#x20;

To prevent any double-spend from a user and to ensure transactions occur in the correct order, each transaction from a given address has an assigned `nonce`. The nonce is the number of transactions from that specific address. The nonce increases by 1 after every transaction. A transaction must but be completed before another one can begin.

### `to`

Transactions have both a sender and receiver. The `to` field is the address of the receiver and can be used to find out which address started the transaction.

### `transactionIndex`&#x20;

When a transaction is added to a block, it is assigned a numbered position inside that block. The `transactionIndex` returns the value of the position of the transaction in the block to which it has been added to.

### `value`&#x20;

Every transaction has an attached value which is the amount that is being transferred from the sender to the receiver. The `value` returns this quantity and is shown in Wei.

### `r,s`&#x20;

Ethereum uses ECDSA (Elliptic Curve Digital Signature Algorithm) to create digital signatures of a transaction. These signatures are used to verify that they are authentic and coming from the correct sender, much like a real signature.

A digital signature can be divided into two numbers - `r` and `s`. These numbers are generated by your private key. By sending `s` through an algorithm, you should get the value of `R` which is used to validate the signature.

### `v`&#x20;

ECDSA uses the `v` value to recover the correct public key when performing validation on the signature of the transactions. It is a combination of&#x20;

* **recovery\_id** - the position on the curve that is used by ECDSA to generate the keys.&#x20;
* **chain\_id** - the Ethereum Network ID (ex: 1 - Ethereum Mainnet).

 61  
guides/what-are-internal-transactions.md
@@ -0,0 +1,61 @@
---
description: >-
  This is an in-depth guide about Internal Transactions on Ethereum and how to
  retrieve them using the Alchemy Transfers API.
---

# What are Internal Transactions?

Internal transactions are transactions that occur between smart contracts. This can also include transactions from a smart contract to an external address when sending ETH to a user. These transactions are labeled internal because every deployed smart contract on the Ethereum blockchain has an assigned internal address.

On the other hand, token transfers are another transaction that occurs between user-created external addresses. An example of this type of transaction is token transfers, such as one user sending an ERC-20 token to another user.

Internal transactions are triggered when an external address calls a smart contract to execute an operation. The contract then will use its built-in logic to start interacting with the other required contracts it needs to complete the operation. Even in a single transaction, a smart contract may need to perform several internal calls to other contracts.

## Internal Transaction Example&#x20;

A typical example of this transaction flow would be the use of a token exchange.

1. **Token Transfer** - A user starts an exchange of an ERC-20 token by sending their tokens to the exchange's smart contract.

2. **Token Transfer** - The exchange's smart contracts will then deposit the tokens to the smart contract connected to the liquidity pool.

3. **Internal Transaction** - The exchange's contract receives the exchanged amount in WETH (wrapped Ethereum) and then sends that amount to the WETH smart contract to be converted to ETH.

4. &#x20;**Internal Transaction** - The exchange's contract sends this ETH to the user's external address to complete the exchange.

## Use-Cases

Internal transactions can provide some vital information for your users. Here are a few use-cases where internal transaction information can be used inside a dApp:

1. &#x20;**Failed Transactions Notifications** -The entire transaction will fail if an internal transaction fails. Informing users exactly where the point of failure helps fix the issue.

2. **Smart Contract Monitoring** - Your deployed smart contract can interact with other contracts via internal transactions. To know when and which contracts it interacts with, you can monitor your smart contract address for any internal transactions.

3. **Blockchain Analytics** - Since internal transactions can be complex, getting insights is helpful. By looking at the number of internal transactions performed by a smart contract, you can understand the popularity and performance of that contract.

4. **Batch Transactions** - If you send a batch of transactions to different sender addresses, you can use internal transactions to more conveniently and securely ensure they reach the right addresses.

## Tracking Internal Transactions

The information found in internal transactions can guide and inform users about their transactions. The tricky part about internal transactions is that they are not stored on-chain and have no cryptographic signatures, such as external transactions. This makes them harder to track and requires additional resources like an archival node.

To get internal transaction information, set the `category` to `internal` like in the example below:

```json
{
  "jsonrpc": "2.0",
  "id": 0,
  "method": "alchemy_getAssetTransfers",
  "params": [
    {
      "fromBlock": "0xA97AB8",
      "toBlock": "0xA97CAC",
      "maxCount": "0x5",
      "excludeZeroValue": true,
      "category": [
        "internal"
      ]
    }
  ]
}
```
