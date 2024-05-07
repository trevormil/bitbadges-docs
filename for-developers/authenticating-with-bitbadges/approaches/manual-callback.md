# Manual Callback

Manual callbacks follow the same approach as the Sign In with BitBadges callback approach, except you implement all the behind the scenes yourself.  See [https://github.com/Blockin-Labs/blockin/tree/main/src/ui](https://github.com/Blockin-Labs/blockin/tree/main/src/ui) for how we implement the Sign In With BitBadges button.

To avoid repeating ourselves, we will explain the behind the scenes below. However, for handling the callback and everything lese that is also done in the SIgn In with BitBadges callback approach, we refer you to [that page](sign-in-with-bitbadges-callback.md).



**How does it work behind the scenes?**

The callback uses **window.opener.postMessage** to pass back the  { message, signature, verificationResponse, secretsProofs } to the parent window upon the message being signed by the user. This allows you (the parent window) to receive everything without any additional user clicks. The child window will auto-close upon signature.

**Security -** It is critical to verify callback origin, source, and well-formed callback data is returned. This is because 1) BitBadges is a centralized service, 2) you should assume in the worst case that the user manipulated the sign in request, and 3) you want to make sure that you receive the correct sign in request. Note that even though you verify origin is from 'bitbadges.io', you will also need to verify message is as intended because messages can come from any BitBadges tab. Read more [https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage).

Read more [https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage) here or see the manual callback implementation.

{% @github-files/github-code-block url="https://github.com/Blockin-Labs/blockin/tree/main/src/ui" %}

