# Non-Indexed Claims

**Non-indexed claims are a special type of claim that has unique properties. Notably, they are autonomous, self-contained, can be fetched on-demand, stateless, does not require any user inputs, sessions, and can function with just a user address / creator parameters.**&#x20;

In other words, nothing is stored, there is no inputs or claim step, and these can be queried / fetched dynamically from source (on-demand) at any time.

For the claim to have these properties, all plugins must have the properties. Some example plugins include:

* Public badge ownership queries
* Minimuum $BADGE balance checks
* Transfer time checks

For example, checking a minimum balance of $BADGE is safe to use because we always know a user's balance at any given time wihtout user interaction and just their address.



Because of these unique properties, you are able to do the following:

* SIWBB requests would not require the "claim step" from the user. We can just check all such information on the go.
* Non-indexed balances meaning that we just lookup balances for any user at any given time. No transfers need to take place. (see picture below).

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>
