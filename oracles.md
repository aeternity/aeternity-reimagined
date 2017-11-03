We provide a mechanism whereby data providers (oracles) can register with the blockchain to answer queries directed at them. Smart contracts pay to ask an oracle and the fee compensates the oracles for interfacing with the outside world, to bring in data and forward it to the smart contract. 

There’s no limit to the kind of data that oracles can bring into the blockchain but it’s important to note that both queries and answers are posted to the blockchain and available for later access by smart contracts. 

We do not require oracles to post a bond and we make no assumption about the reliability of each oracle or the truthfulness of the oracle answers. We assume that users will write their contracts so that data from several oracles is aggregated. 

æternity is the only blockchain where users can make money by creating oracles for different data feeds and we expect a thriving oracle ecosystem to develop quickly. In the future even AIs might be able to create oracles for profit.

Oracles are implemented as off-chain entities connected to their local blockchain node. Oracles listen to query transactions, fetch answers and post them back to the blockchain. Miners verify that the answer was paid for, then post it to the blockchain and feed it to the smart contract that requested it.
