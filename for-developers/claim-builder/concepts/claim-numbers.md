# Claim Numbers

By default, we use an incrementing claim number system for standard claims. For example, claim #1, then claim #2, etc.&#x20;

However, certain implementations may custom assign claim numbers, which can be used to implement custom logic, such as distributing specific tokens.

Only one plugin is allowed to assign claim numbers which is determined by the **assignMethod** of the claim. If the assignMethod === a plugin's unique instance ID, we allow it to assign claim numbers.

<figure><img src="../../../.gitbook/assets/image (186).png" alt=""><figcaption></figcaption></figure>
