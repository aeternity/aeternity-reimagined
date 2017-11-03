æternity is a decentralized, public blockchain that uses the GHOST consensus protocol, with Proof-of-Work (PoW) for security and Proof-of-Stake (PoS) for governance. æternity is an account-based system, like Ethereum, and does not use Bitcoin-style unspent transaction outputs (UTXO).

A blockchain is a public ledger that needs to be easily verifiable. Maintaining the full data structure of account balances is easily beyond the capabilities of a verifier with limited amounts of RAM or resources. We have adopted Authenticated Dynamic Dictionaries to considerably reduce verifier load and speed up verification by including proofs in each block that all transactions resulted in the correct modification of the account balance structure. 

Verifiers can hold a short digest (hash) of the data structure to verify each proof and compute the new digest that corresponds to new account balances. The verifier can perform these checks and updates without trusting the miner and verifier storage is minimal. 32 bytes suffice for a digest and each proof is a few hundred bytes long.

The æternity token (AE) is used as payment for any resources that users consume on the platform, e.g. sending payments, using oracles, etc. The distribution of AE in the genesis block will be determined by a smart contract hosted on Ethereum. More AE will be created via mining.

We use Cuckoo Cycle as the ASIC-resistant mining algorithm to avoid centralizing mining power in the hands of a few large mining pools.

The next generation blockchain solutions need to provide high performance and scaling if the goal is to compete with and even replace existing centralized mechanisms, e.g. VISA and MasterCard networks that process thousands of transactions per second and provide near-instant payment confirmations.

This can be achieved with faster consensus and data storage on the blockchain (on-chain scalability) as well as by not storing all the data on the blockchain (off-chain scalability). 

We propose to achieve off-chain scalability by using state channels where only the channel opening and closing data is stored on the blockchain and smart contracts and data can be exchanged privately and securely between two parties, with no limitation on the speed of the exchange. 

We are already hard at work on the design of a faster consensus protocol and a sharding mechanism, with launch planned after the release of our mainnet.

Ethereum’s Solidity is the most popular smart contracts language at this time and plenty of programmers are familiar with it. We make it easy to migrate to æternity by running Ethereum smart contracts without connecting to or being part of the Ethereum network. At the same time, it has been proven that Solidity is not the safest smart contracts language. We plan to release a safer and easier to verify smart contracts language after launching the mainnet.

Moreover, EVM smart contracts are very limited by the fact that they have no way to access data that is not present on the blockchain. This restriction has been alleviated by the use of external data providers (oracles), which publish data on the blockchain and make it available to smart contracts. A lot of the current oracle designs focus on ensuring the oracle answer is true but do so at the expense of flexibility, usability and higher cost. Knowing whether or not X is the new President is important to a narrow segment of blockchain users, e.g. those interested in betting on the US presidential election. (VLAD: This last sentense makes no sense to me. Also, the accuracy of decentralized oracles must be very high on any subject in order for prediction markets to work in a decentralized way.)

We provide much more flexible and universal oracles, making it possible to bring in current weather, asset prices, logistics information, etc. to smart contracts running on æternity, rather than yes or no (binary) answers. This makes it possible to build a host of applications on top of æternity, not just prediction markets.

We have no way to predict the kinds of applications that will be built on top of æternity. We envision that changes will be needed to the core blockchain software but want these to be driven by our community of users and its application needs. We provide a governance approach where users can vote on core software changes and system parameters (e.g. block size or frequency), with votes counting proportionally to the number of tokens each user is holding. Reaching consensus on governance inquiries will be handled by æternity's PoS system.

Finally, we provide a rich internal API to allow clients to connect to their local node. The API enables clients to listen for transactions, post transactions and generally interact with the blockchain in a convenient and user-friendly way. 

We elaborate on the above in the rest of the paper.
