# Archived Collections

Collections can be archived using the **isArchivedTimeline**. Before any transaction, we check if the collection is archived at the current time. If it is, all transactions will fail. The next successful transaction that can be processed must be one that unarchives the collection. The updatability of the **isArchivedTimeline** is controlled by the **canArchiveCollection** permission.

Use Cases: This can potentially be used for a few reasons

* Security: Pause all transactions if you believe the collection's security is at risk
* Sunsetting: Sunset the collection to be idle but still verifiable and public on-chain.

Note this doesn't delete the collection, just makes it read-only.
