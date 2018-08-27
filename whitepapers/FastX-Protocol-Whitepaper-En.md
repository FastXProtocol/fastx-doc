# FastX - An Open Protocol for Secure and Fast Crypto-goods Exchange

## Abstract

This protocol aims to bring fast and low fee token exchanges to any dApps running on Ethereum. To help such applications making full use of the existing technology framework, while conveniently integrating the blockchain technology. FastX eliminates the need for each application to develop independently, to operate and the additional burden of maintaining their own exchange systems, and through direct wallet-to-wallet transactions, it increases the liquidity sharing between dApps. The protocl adopts Plasma technology, which effectively combines the on-chain security and the high speed and low fee transactions off-chain. FastX provides users with a safe secure, high speed, and low fee trading experience.

## Introduction

There have been all kinds of crypto exchanges got hacked and even went bankruptcy, from Mt. Gox, to Bitfinex, to the latest Bancor network hacks. In the first half of 2018 alone, the exchanges has been stolen over 730 million US dollars, which is over 3 times more that that of 2017. Everyone is actally got affected by the market crashes caused by those big hacks. It is because crypto exchanges, just like crypto mining, are at the core of the crypto world.

Then a new type of exchanges called decentralized exchanges, or DEX, are trying to solve the security issues of centralized exchanges. Instead of pooling all users' assets together in the exchange's wallets, DEX allows users to exchange tokens directly on the blockchain. 

But one problem with DEX is that its trading speed is limited by the transaction confirmation time of the underlying blockchain, which is way much slower than centralized exchanges. For example, a single block confirmation takes about 14 seconds on Ethereum, comparing to transactions are finished in milli-seconds in the centralized exchange. 

Another issue with DEX is that each trade needs to pay not only fees to the exchange, but also transaction fees to the underlying blockchain. And what makes it even worse is that the more trades on DEX, the higher transaction fees users have to pay because users have to compete with each other, by paying higher fees to incentivize miners to process their transactions first.

To solve all the above issues, we are proposing a new decentralized token exchange protocol on Ethereum, called FastX.

## FastX Protocol

### Overview

Here, we propose an open token exchange protocol on Ethereum, FastX. It is built with Plasma[^plasma] framework, and consists mainly of 3 components, which are Root-chain Smart Contract(RSC), the child-chain operators, and the clients. Each component can be run by different teams or entities, and together they collaborate to facilitate token tradings under the specifications of FastX protocol. Instead of building everything from scratch, FastX is designed to be easily integrated with existing web and mobile apps, so they can take advantage of blockchain technologies with existing techology stacks and infrastructures. 

While current decentralized exchanges managed to move order matching off-chain (like 0x[^zero_x]), FastX is able to offload both order matching and transacton settlement away from the blockchain. By extending Plasam, FastX added new types of transactions which allow users to create maker orders directly from their clients, sign the orders and broadcast them to FastX network. Once other clients receive the maker orders from the network, they validate the orders, and filter them with users' criterias. Once there are matches, takers can sign the orders directly from the clients and broadcast to the network. Child chain operators include validate transactions to the block, calculate the Merkle root of the transaction tree, and periodically commit the root hash to RSC on Ethereum. Once confirmed on Ethereum, the committed hash can be verified by any user. If someone tries to steal money from FastX, anyone can challenge the attacker by sending a transaction to RSC with the proof attached, and RSC makes the verification and final decisions to block the money stealing and to reward the challenger with the deposits from the attacker.

### Features

Besides features like safe and secure of users' assets in DEX, high transaction throughput, and low transaction fees which are provided by Plasma, FastX also provides the following extra benefits.

#### ERC20 and ERC721 Supports

ERC20 and ERC721 are the most widely used tokens on Ethereum. We added new attributes `contractAddress`, `amount`, `tokenId` to the Transaction data structure, and extended `deposit` and `exit` in Plasma to support them.

#### Built-in Order TX

To improve the user experience of order matching, we added new types of _Order_ transactions. So, a user can create a maker order by specifying the token and amount he wants to sell in his client, signs the order and broadcasts to the network. Other clients pick up the order from the network, and allows a taker to then sign the whole order tx in her client and send back to the network for settlement. This way no third-party relays are needed any more.

#### Transaction Timeout

To facilitate the liquidity of the network, and prevent someone to sign the order and wait long enough until making profts, We added `expireTime` to the _Order_ transaction. Once the transaction expires, it cannot be included into the block even it is signed both by the maker and the taker.

#### Instant Tx Finality

It is crucial for a better UX to have a very responsive UI, and that asks for transaction confirmations to have very low latencies. We are working on a solution where child chain operators sign all pending transactions, which promise to include those transactions in the next few blocks. This way, the child chain can provide almost instant transaction finalities.

#### In-app Exchange SDK

To allow users to easily use a dApp, even without its own token, FastX provides a client-side SDK for developers to easily integrate with. So a user can buy dApp's token within the application with various kinds of tokens the user owns. A user can even exchange tokens with other users inside of the app without having to log on a third-party exchange website.




[^plasma]: https://plasma.io
[^zero_x]: httpS://0xproject.io