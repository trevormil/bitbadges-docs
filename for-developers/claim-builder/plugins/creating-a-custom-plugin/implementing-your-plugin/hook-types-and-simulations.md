# Hook Types and Simulations

### Timing of Hooks

BitBadges sends two types of hooks (during processing hooks and success hooks- after successful claim). Certain settings in the creation form can be set to customize how or if we send these. You may choose to receive or ignore them.

* Processing hooks (\_attemptStatus = "executing") are checked during the execution of the claim, and the results could influence whether the claim succeeds or not.
* Success hooks (\_attemptStatus = "success") are only sent after the claim is successful and cannot affect the outcome of the claim. Typically used for post-claim actions or success logic. If a 200 OK is not received, we will use exponential backoff to retry until successful.

<figure><img src="../../../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Simulations

```
NOTE: Catching simulations is important. 

You do not want to execute success logic on a dry run.
```

We allow users to simulate claims as a dry run before they actually submit for real. The scope of the dry run is left up to you, but we recommend as a rule of thumb is that if the user successfully simulates, they are expected to always pass at execution time.

To determine whether you are receiving a simulation hook or a "for real" hook, you can use the \_isSimulation flag that is passed. The claimAttemptId will also be empty, but the rest of the payload should remain the same.&#x20;

For building scalable plugins, we also recommend implementing some sort of caching mechanisms to avoid double processing your logic (cache at simulation time and reuse at actual time).

Simulations done via the simulate endpoint rather than the complete claim API endpoint. The BitBadges site will always simulate once before submitting, but you should not depend on every claim attempt having a prior simulation.

It is not recommended, but you can select to not receive simulation hooks at all in the usage settings when creating your plugin in the developer portal.
