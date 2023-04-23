# Use CosmWasm

IMPORTANT: Note that when CosmWasm interacts with the badges module (i.e. creates and broadcasts a [Tx Msg](../need-to-know/tx-msg-interfaces.md)), the creator field (calling address) of the Msg will always be the contract's address / account ID number, NOT THE ORIGINAL USER's address / account ID number. In other words, Cosmos SDK / CosmWasm does not have a Solidity **tx.origin** equivalent.

For example, if you want to create a function which updates the permissions of a badge collection according to some logic, this would only be possible if the contract is the manager. It would not work if the original user calling the contract is the manager because technically, the contract submitted the Msg, not the original user.&#x20;



Some common workarounds around include:

\-Give the contract the manager role

\-Have users approve the contract to transfer w/ MsgSetApproval

### Tutorial

TODO
