# Draft v0.1

Author: Joel Reymont

## Abstract

To replace existing centralized mechanisms, a blockchain needs to scale to thousands of transactions per second in a decentralized and trustless way, as well as make use of real world data. For some transactions a true instant confirmation is required and their data is best kept private, off the chain.

We present Aeternity, a multi-asset blockchain for decentralized and trustless applications that can seamlessly access real-world data. We offer scalability and privacy via state channels. We run Ethereum smart contracts that can use data supplied by off-chain data providers (oracles). 

To avoid Bitcoin hard-forking issues, we provide a built-in governance mechanism that enables users to vote on future upgrades to core functionality, as well as to tune system parameters. 

Last but not least, we include an identity and naming system, and a mechanism to create custom tokens.

Our software is written in Erlang/OTP, a fault-tolerant and industrial-strength platform used to develop telecom software. Erlang allows us to release new features and bug fixes at a rapid clip while maintaining a very stable network.


## Introduction

This paper is broken into several parts. First we give a general overview of the system and the workings of the blockchain. Then we describe the mechanisms used for consensus, state channels, oracles and smart contracts. Finally, we describe governance by voting and the name system, then conclude with a forward outlook.


## Overview

Aeternity is a decentralized and public blockchain that uses the GHOST consensus protocol, with proof of work (PoW) for security and proof of stake (PoS) for governance. Aeternity is an account-based system, like Ethereum, and does not use Bitcoin-style unspent transaction outputs (UTXO).

A blockchain is a public ledger that needs to be easily verifiable. Maintaining the full data structure of account balances is easily beyond the capabilities of a verifier with limited amounts of RAM or resources. We have adopted Authenticated Dynamic Dictionaries to considerably reduce verifier load and speed up verification by including in each block proofs that each transaction resulted in the correct modification of the account balance structure. 

Verifiers can hold a short digest (hash) of the data structure to verify each proof and compute the new digest that corresponds to the new account balances. The verifier can perform these checks and updates without trusting the miner and verifier storage is minimal. 32 bytes suffice for a digest and each proof is a few hundred bytes long.

The Aeternity token (aeon) is used as payment for any resources that users consume on the platform, e.g. sending payments, using asking oracles, etc. The distribution of aeon in the genesis block will be determined by a smart contract hosted on Ethereum. Further aeon will be created via mining.

We use Cuckoo Cycle as the ASIC-resistant mining algorithm to avoid centralizing mining power in the hands of a few large mining pools.

A blockchain needs to be highly performance and scalable if it’s to replace existing centralized mechanisms, e.g. VISA and MasterCard networks that process thousands of transactions per second and provide near-instant payment confirmations. 

This can be achieved bydone faster consensus and quicker storing of data on the blockchain (on-chain scalability) as well as by not storing all the data on the blockchain (off-chain scalability). 

We propose to achieve offn-chain scalability by using state channels where only the channel opening and closing data is stored on the blockchain and smart contracts and data can be exchanged privately and securely between two parties, with no limitation on the speed of the data exchange. 

We are already hard at work on the design of a faster consensus protocol and a sharding mechanism, with launch planned after the release of our mainnet.

Ethereum’s Solidity is the most popular smart contracts language at this time and plenty of programmers are familiar with it. We make it easy to migrate to Aeternity by running Ethereum smart contracts without connecting to or being part of the Ethereum network. At the same time, it has been proven that Solidity is not the safest smart contracts language. We plan to release a safer and easier to verify smart contracts language after launching the mainnet.

EVM smart contracts are very limited by the fact that they have no way to access data that is not present on the blockchain. This restriction has been alleviated bypossibilities unless they can access the real-world via external data providers (oracles), which publish data on the blockchain, to make it available to smart contracts. A lot of the current oracle designs focus on ensuring the oracle answer is true  but do so at the expense of flexibilityusability. Knowing whether or not X is the president is mostly important to a narrow segment of blockchain users, e.g. those interested in betting on the US presidential election. 

We provide much more flexible and general oracles, making it possible to bring in current weather, asset prices, logistics information, etc. to smart contracts running on Aeternity, rather than yes or no (binary) answers. This makes it possible to build a host of applications on top of Aeternity, not just prediction markets.

We have no way to predict the kinds of applications that will be built on top of Aeternity. We envision that changes will be needed to the core blockchain software but want these to be driven by our user community and its application needs. We provide a governance approach where users can vote on core software changes and system parameters (e.g. block size or frequency), with votes counting proportionally to the number of tokens each user is holding.

Finally, we provide a rich internal API to allow clients to connect to their local node. The API enables clients to listen for transactions, post transactions and generally interact with the blockchain in a convenient and friendly way. 

We elaborate on the above in the rest of the paper.

## Consensus 

Aeternity uses a Proof-of-Work (PoW) consensus mechanism. Certain system variables will be determined via voting (governance). For the PoW algorithm we use John Tromp's Cuckoo Cycle which is memory bound.

John Tromp writes about his work:

_Cuckoo Cycle is an instantly verifiable memory bound PoW that is unique in being dominated by latency rather than computation. In that sense, mining Cuckoo Cycle is a form of ASIC mining where DRAM chips serve the application of randomly reading and writing billions of bits._

The original Nakamoto consensus assumes that block propagation delays are negligible compared to the time required to generate a block. This assumption breaks once we start generating blocks every few seconds instead of minutes. Instead, we adopt GHOST, the Greedy Heaviest Observed Subtree  protocol, a more secure and scalable system that is suitable for our needs. GHOST boosts block production speedup by a factor of more than 40 with no security loss. 

This protocol takes into account not just the “length” of the tree, calculated as the sum of difficulty, but the combined “length” of all blocks rooted at the current block. Blocks that are off the main chain still contribute to the chain’s irreversibility. GHOST exploits the work invested in conflicting blocks to enhance their ancestor’s security, by making it harder to omit the ancestor from the main chain.

We plan to switch to an even more scalable consensus protocol in the future. 

## State Channels

State channels offer a way to scale “off chain” by only storing channel opening and closing information on the blockchain. Two parties deposit tokens into a channel when opening it and the sum amount of two deposits is only amount of tokens that can be used within a channel. The state of the channel is the current balance of each party, co-signed by both parties. There can be multiple exchanges of state between the parties but only the last undisputedlast state becomes the final closing state of the channel. Each message in the channel carries an ever-increasing counter and the message with the highestr counter value is considered to be the last state.

We distinguish between payment and state channels. Payment channels are use a simple setup where small quantities of tokens are exchanged incrementally and there’s no conflict resolution, and true state channels that run smart contracts and resolve conflicts on the blockchain.

Payment channels are suitable for providing a movie playing service, where the movie player sends short chunks of video in exchange for a small payment for the next chunk. No smart contracts are involved in the setup and operation of a payment channel. Assuming that the incremental payments are small enough, a single such payment is all that a party stands to lose so no conflict resolution is required. 

State channels exchange smart contract code and state with each message in the channel. All messages are signed by both parties and both parties can verify that the state is correct by executing the smart contract included in the message. A set of fingerprints of all contracts that can be exchanged in the channel is included in the channel opening transaction.

In case of conflict, either party can post the latest cosigned state and the code of contract to the blockchain and the blockchain will verify correctness of the state by executing the smart contract. The blockchain can verify that the contract is valid by checking its fingerprint against those supplied when opening the channel.

Each channel message carries an optional “timelock” that expires a given number of blocks in the future. Assuming that one of the parties has decided to unilaterally close the channel and post a state to the blockchain as the latest, the timelock gives the other party time to post a co-signed state with a higher counter value. This is important, for example, in the case where one of the parties lost internet connectivity.

Each channel established between two parties (one-to-one) holds its own separate state. It’s often beneficial to establish a many-to-one channel, e.g. with many buyers connecting to a single seller. We enable this scenario by allowing the opening of state channels to off-blockchain applications. These applications can be written in any language but still need to connect to a local blockchain node to listen for and reply to state channel events.

We leave state channel networks for future work.

## Oracles

We provide a mechanism whereby data providers (oracles) can register with the blockchain to answer queries directed at them. Smart contracts pay to ask an oracle and the fee compensates the oracles for interfacing with the outside world, to bring in data and forward it to the smart contract. 

There’s no limit to the kind of data that oracles can bring into the blockchain but it’s important to note that both queries and answers are posted to the blockchain and available for later access by smart contracts. 

We do not require oracles to post a bond and we make no assumption about the reliability of each oracle or the truthfulness of the oracle answers. We assume that users will write their contracts such that data from several oracles is aggregated. 

Ours is the only blockchain where users can make money by creating oracles for different data feeds and we expect a thriving oracle ecosystem to develop quickly.

Oracles are implemented as off-chain entities connected to their local blockchain node. Oracles listen to query transactions, fetch answers and post them back to the blockchain. Miners verify that the answer was paid for, then post it to the blockchain and feed it to the smart contract that requested it.

## Smart Contracts

Despite all the security and safety issues associated with Solidity programming, it has become the most popular smart contract language in the blockchain space. We are looking to allow Solidity smart contracts to run on top of Aeternity with no modification.

Aeternity blockchain nodes will not become part of the Ethereum network but we will leverage the large body of smart contract code written in Solidity and the skills of the many programmers who know how to write it.

We will also try to improve on the Ethereum VM wherever possible, e.g. by only charging for space actually used by a smart contract. 

## Governance

We have no way to predict the kinds of applications that will be built on top of Aeternity. We envision that changes will be needed to the core blockchain software. We want the buy-in from our user and investor community before making these changes. We also want the community to propose blockchain extensions but don’t want to be the single arbiter to judge whether or not to make them happen.

We provide a governance approach where users can vote on core software changes and system parameters (e.g. block size or frequency), with votes counting proportionally to the number of tokens each user is voting with.

The tokens will be locked up for the period of the vote and it will not be possible to use them same tokens to vote more than once. The prevailing side will decide if the features will be implemented or rolled out, or changes to the system parameters made. This will cause a “hard fork”, with the owners of the nodes voting for the changes needing to upgrade their software.

## The Multi-Asset Blockchain

We want to enable applications where different kinds of real-world and virtual goods can be bought and sold on top of the Aeternity blockchain. Let’s call them child tokens to make it clear they all have the aeon as their parent.

Alice may have a fixed quantity of virtual apples that she wants to sell. She will use a simple API to define a new “virtual apples” token with a limited quantity, and offer to sell this token in exchange for aeons. Alice will not be able to sell more virtual apples than she initially promised but users will be able to use the new virtual apples token as if it was aeon. The system will automatically decrease Alice’s inventory of virtual apples and credit her account in aeons as users buy virtual apples from her.

Bob wants to sell real-world goods while keeping track of inventory on the Aeternity blockchain. Bob will set up a new widget token without limiting the quantity of widgets in existence. Bob will still be able to sell his widgets in exchange for aeons. Bob will post to blockchain the proof of availability of his real-world widgets which will allow users to confirm this before buying widget tokens from Bob. The Aeternity blockchain will serve as proof of ownership and will let widget owners trade then with each other.

Finally, having a built-in exchange for the child tokens should let miners and others to accept any child token as payment for their services.

## Name System

Blockchain systems suffer from unreadable addresses used to access accounts, smart contracts, etc. We feature a name system that is both decentralized and secure, and supports human-friendly names. The blockchain state includes a mapping from unique human-friendly strings to hashes, digests, fingerprints and addresses. 

Users will be able to send money to “Alice” or “Bob”, assuming that both Alice and Bob took the time to secure the name mapping. We will likely hold an auction for the initial registration of name mappings and may include a market where name mappings can be bought and sold.

## Future Work


We believe a blockchain needs to reach VISA and MasterCard throughput of thousands of transactions per second to replace those systems.  Sharding and a higher performance consensus protocol are required to make this  happen. We have evaluated the latest research and think that [Omniledger](https://eprint.iacr.org/2017/406.pdf) is the best direction for future work, sans UTXO and with PoW.
