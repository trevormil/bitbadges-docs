# Hook Types and Simulations

### Timing of Hooks

Plugins are processing hooks — they are checked during the execution of the claim, and the results influence whether the claim succeeds or not. The `_attemptStatus` will be "executing" during claim processing.

### Simulations

```
NOTE: Catching simulations is important.

You do not want to execute success logic on a dry run.
```

We allow users to simulate claims as a dry run before they actually submit for real. The BitBadges site will always simulate once before submitting, but you should not depend on every claim attempt having a prior simulation.

The scope of the dry run is left up to you, but we recommend as a rule of thumb is that if the user successfully simulates, they are expected to always pass at execution time.

To determine whether you are receiving a simulation hook or a "for real" hook, you can use the \_isSimulation flag that is passed. The claimAttemptId will also be empty / blank, but the rest of the payload should remain the same.

You can select to not receive simulation hooks at all in the usage settings when creating your plugin in the developer portal.
