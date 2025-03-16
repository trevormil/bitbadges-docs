# Standard vs On-Demand

Claims, at their core, are just criteria checks, but there are two ways we can check this criteria. The first is standard claims which require a "complete claim" process or action:

* Criteria is checked at completion time
* Claim numbers are assigned
* Can maintain statefulness
* Ledger of users who have claimed

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

The second is on-demand (sometimes described as non-indexed). These are a special type of claim that has unique properties. Notably, they are autonomous, self-contained, can be fetched on-demand, stateless, does not require any user inputs, sessions, and can function with just a user address / creator parameters.

The critieria is not indexed anywhere but rather calculated on-demand.

* No indexing
* No claim numbers
* No verifiable list of users who have claimed
* Limited in feature set because you need to be able to check criteria at any time, so you cannot use authenticated sessions or other apporaches
* Calculated in real-time
* No success webhooks

For example, checking a minimum balance of $BADGE is safe to use on-demand because we always know a user's balance at any given time wihtout user interaction and just their address. Another common on-demand check is badge ownership as shown below.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
