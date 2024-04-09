# ⚓ Anchors

Anchors, in the context of blockchain technology, refer to specific transactions utilized to validate the presence or absence of particular data on the blockchain. These transactions serve as cryptographic proofs, verifying the existence of data at a given point in time. Anchors play a crucial role in establishing the integrity and immutability of data stored on the blockchain, enhancing transparency and trust within decentralized systems.

This is facilitated through the x/anchor module which simply has one message (MsgAddCustomData) that simply lets you store a string. It will store it on-chain with a timestamp and the address that inititated the transaction.
