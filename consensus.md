æternity uses a Proof-of-Work (PoW) consensus mechanism. Certain system variables will be determined via voting (staking?) (governance). For the PoW algorithm we use John Tromp's Cuckoo Cycle.

John Tromp writes about his work:

_Cuckoo Cycle is an instantly verifiable memory bound PoW that is unique in being dominated by latency rather than computation. In that sense, mining Cuckoo Cycle is a form of ASIC mining where DRAM chips serve the application of randomly reading and writing billions of bits._

The original Nakamoto consensus assumes that block propagation delays are negligible compared to the time required to generate a block. This assumption breaks once we start generating blocks every few seconds instead of minutes. In order to address this issue, we adopt GHOST, the Greedy Heaviest Observed Subtree protocol, a more secure and scalable system that is suitable for our needs. GHOST boosts block production by a factor of more than 40 with no security loss. 

This protocol takes into account not just the “length” of the tree, calculated as the sum of difficulty, but the combined “length” of all blocks rooted at the current block. Blocks that are off the main chain still contribute to the chain’s irreversibility. GHOST exploits the work invested in conflicting blocks to enhance their ancestor’s security by making it harder to omit the ancestor from the main chain.
