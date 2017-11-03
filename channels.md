State channels offer a way to scale “off-chain” by only storing channel opening and closing information on the blockchain. Two parties deposit tokens into a channel when they open it and the sum of two deposits is the only amount of tokens that can be used within that channel. The state of the channel is the current balance of each party, co-signed by both parties. There can be multiple exchanges of state between the parties but only the last undisputed state becomes the final closing state of the channel. Each message in the channel carries an ever-increasing counter and the message with the highest counter value is considered to be the last state.

We distinguish between payment and state channels. Payment channels are used as a simple setup where small quantities of cryptocurrency are exchanged incrementally and there is no conflict resolution. True state channels can run smart contracts and resolve conflicts on the blockchain.

Payment channels are suitable for providing, e.g., a video playing service, where the video player sends small chunks of video in exchange for small payments. No smart contracts are involved in the setup and operation of a payment channel. Assuming that the incremental payments are small enough, a single such payment is all that a party stands to lose so no conflict resolution is required. 

State channels exchange smart contract code and state with each message in the channel. All messages are signed by both parties and both parties can verify that the state is correct by executing the smart contract included in the message. A set of fingerprints of all contracts that can be exchanged in the channel is included in the channel opening transaction.

In case of conflict, either party can post the latest cosigned state and contract code to the blockchain and the blockchain will verify correctness of the state by executing the smart contract. The blockchain can verify that the contract is valid by checking its fingerprints against those supplied by the users the channel was opened.

Each channel message carries an optional “timelock” that expires after a given number of blocks in the future. Assuming that one of the parties has decided to unilaterally close the channel and post a state to the blockchain as the latest, the timelock gives the other party time to post a co-signed state with a higher counter value. This is important, for example, in the case where one of the parties lost internet connectivity.

Each channel established between two parties (peer-to-peer) holds its own separate state. It’s often beneficial to establish a many-to-one channel, e.g. with many buyers connecting to a single seller. We enable this scenario by allowing opening of state channels to off-blockchain applications. These applications can be written in any language but still need to connect to a local blockchain node to listen for and reply to state channel events.

We leave state channel network for future work.
