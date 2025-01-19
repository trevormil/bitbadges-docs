# Claim Numbers

## Custom Claim Numbers

By default, we use an incrementing claim number system. For example, claim #1, then claim #2, etc. However, certain plugins may require specifying claim numbers in order to function. Claim numbers can be used to distribute specific badges. They are not applicable to address lists or non-indexed (see below) actions.

Only one plugin is allowed to assign claim numbers which is determined by the **assignMethod** of the claim. If the assignMethod === a plugin's unique instance ID, we allow it to assign claim numbers.
