# Parameters

Each plugin can configure the expected schema of parameters inputted by the claim creator and the end user. These will all be available in the payload of your handler in addition to contextual information like address, attempt ID, etc.

```json
{
    ...context,
    ...publicParams, // Public to the end users
    ...privateParams, // Private to BitBadges and the claim creator
    ...userInputs
}
```

Parameters will be passed to your backend handler in the payload.

<figure><img src="../../../../../.gitbook/assets/image (163).png" alt=""><figcaption></figcaption></figure>