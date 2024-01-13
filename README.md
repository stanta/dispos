# DistPOS -  Distributed  POS  consensus
- Stanislav Takt, [2018-2021]
- https://www.linkedin.com/in/taktaev
- https://github.com/stanta 

## Decentralized Money -> Distributed Money
Bitcoin, Ethereum and blockchains with a single consensus is just the first step towards the decentralization of money. These are still very centralized solutions. The next step, it will become obvious, every company and even every person will start issuing their own tokens - IOUs[1]. This means that the number of blockchain users will be measured in hundreds of millions, and the number of transactions in the billions. It is obvious that existing blockchains with centralized consensus will not be able to withstand such a load without centralization, as we already see in the example of blockchains like Near, Solana, Binancechain, which destroys an idea of ​​​​decentralization. It is logical to assume that there will be a massive increase in the number of "local" blockchains - both geographically (blockchains of a concrete city, village) and socially - each community will have its own blockchain.
Bitcoin and Ether, of course, will remain, like Visa and Mastercard, but why should I, as a user, even in New York, know the entire transaction history of the whole world, transactions in some Russian village or Chinese community? It is enough for me, when accepting a transaction, to know that some community is vouching for it, enough to cover if the obligations are not fulfilled.
Here, the problem of exchanging transactions between these blockchains inevitably arises.

## Limitations of Logical Consensus Centralization in Blockchain

Logical consensus centralization is a major limitation to the scalability of blockchains. “A single blockchain for the whole world” is a fantasy, a chimera, a too overloaded solution turns out, a ready-made center of vulnerability, a point of failure.
Such a Blockchain has limitations in scaling, as the system falls under the Amdahl law[2], since the transition of the system to the next state (mining a new block) is carried out strictly sequentially after a consensus has been reached between 51% of the system nodes. Synchronization time (sending a new block) between all nodes of the system is not equal to zero. With an increase in the number of blocks or the volume of transactions in them, this time will increase. Thus, with an increase in the number of users and transaction traffic, the system simply will not have time to switch to a new state more often than once every few seconds. Apparently, the current timing of Ethereum blocks look like the practical limit of synchronization time on existing communication networks.
 Now this is actually solved by centralized mining centers, when all servers are located side by side in the same building. But this contradicts the very idea of ​​decentralization. Whoever controls the centralized pool of nodes also controls the consensus.
Conclusion - we need a fractional, distributed consensus.


Existing L2 solutions such as PolkaDot, Cosmos, etc follow the path of "star" decentralization - when many sidechains / parachains are created with their own consensus, synchronized through a central relay chain. Obviously, such a solution only transfers the point of vulnerability of the centralized control of the blockchain to the relay chain. At the same time, parachains cannot control the consensus in the central relay chain in any way.
However, the XCM roadmap for the polkadot protocol plans to exchange transactions directly between parachains[3]. 

## Blockchain with Distributed Consensus 

A solution is proposed that is similar in architecture to a network of email servers, where “local blockchains” can be represented by mail servers. 
The solution can also be presented as an analogue of the banking system before the era of central banks. Then it was much more decentralized, banks could establish correspondent relations directly, peer-to-peer. Banks opened correspondent accounts for each other, credited them with assets (physically transported gold to each other's vaults), for a certain amount. Then special employees made sure that there was no significant imbalance in the settlements on correspondent accounts. Accordingly, if the settlements were skewed to one of the parties, the other party asked to increase the reserves on the correspondent account, otherwise it could suspend the settlements. Such is the handmade proof of stake. 


### Node-Level Implementation
Analog as POS[4], but with extension: Each node stakes (stake) and can sign transactions within this stake without synchronization with other nodes. If these transactions are later rejected by other nodes, then the funds are debited from the node's collateral.
If a transaction needs to be signed for more than the current collateral, the node will encourage other available nodes to underwrite the transaction. If they agree and give guarantees, the transaction is carried out, otherwise it is rejected.

### Interchain  Level
To implement the exchange of transactions between blockchains, you need to enter the chain-id in the address structure, similar to the email domain.
![DistPOS Schema](https://github.com/DistPOS/distpos/blob/main/DisPOS.jpg?raw=true)
#### Step 1 (look pict)
When forming an outgoing transaction to an external chain, the transaction is sent to the mempool of the “local” chain node with the addition of the addressable chain-id.
#### Step 2
If a node, when forming a block, encounters a transaction in the mempool to an external address (not with its own local chain-id), then it looks in the routing table, where each chain-id is associated with the addresses of the starting nodes of this chain. The node sends the transaction to the mempool of any node from this list, while adding “its” chain-id to the sender address of the transaction, as it happens in SMTP.
#### Step 3
The receiving node, when it detects a transaction from an external blockchain in its mempool, before including it in the block, similarly accesses the routing table and asks the sending node for the proof of the Merkle tree for the sent transaction. 
#### Step 4
If the proof is received (that is, the transaction is confirmed by the sending node), the receiving node proceeds to verify the content of the transaction.
We can say that the receiving node acts as a “light client” of the sender blockchain: “If a light client wants to determine the status of a transaction, it can simply request a Merkle proof showing that a particular transaction is in one of the Merkle trees, the root of which is in the header block for the main chain.” [5]
#### Step 5
Further verification of the transaction can be set at the level of smart contracts. It can be checked whether there are enough staked resources on the correspondent account to debit this transaction. Additional checks on the reputation of the blockchain that sent this transaction and others, at the choice of the receiving party, can be performed.
Next, the execution of msg.data is analyzed if they were transferred to the smart contract on the receiving side. 
#### Step 6
If all checks are passed, the transaction is included in the receiving blockchain.
  

This architecture is much simpler than the proposed XCM from polkadot and does not require changing the format of transaction messages, only expanding the address structure. On the example of efirimua addresses, it can be a hex code to 'x' symbol in the URL: 0x63ea7F78107dF4C6fd8b17e7b05b1b3D41a5B1Ab -> e2e4x63ea7F78107dF4C6fd8b17e7b05b1b3D41a5B1Ab, where e2e4 - conditional code of local blockchain.

## Conclusion.
Thus, we make a transparent bridge between different chains, which makes it possible to create a peer-to-peer blockchain network without a central relay chain. 




________________
- [1] https://bit.ly/IOU_WP
- [2] https://en.wikipedia.org/wiki/Amdah’s_law 
- [3] Cross-Consensus Message Format (XCM) https://wiki.polkadot.network/docs/learn-crosschain/ 
https://github.com/paritytech/xcm-format)
- [4] https://en.wikipedia.org/wiki/Proof_of_stake 
- [5] https://blog.ethereum.org/2015/11/15/merkling-in-ethereum/ 
https://easythereentropy.wordpress.com/2014/06/04/understanding-the-ethereum-trie/
