# Asynchronous Queue Processing

An important concept to understand when building plugins is that BitBadges processes plugins asynchronously before the claim is fully processed. Typically, there will only be a couple seconds between processing times, but claim state is not guaranteed to stay the same between your individual plugin processing and claim processing.

**However, this means that you should NOT depend on the overall claim state in any form when implementing your processing hook logic (i.e. the state on BitBadges' end like number of claims processed).**

You may manage your own state on your end, and we have settings for you to set state in an eventually consistent manner on BitBadges end (see handler response formats below).

This mainly applies for processing hooks. For success hooks, you can assume that the respective attempt has succeeded already. Parameters are okay to depend on. We will pass the parameters of your plugin to you via the handler. If you need other plugins' parameters, you may query them via the API, but ensure the claim versions match to avoid race conditions.
