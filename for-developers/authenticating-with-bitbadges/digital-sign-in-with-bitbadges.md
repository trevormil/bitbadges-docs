# Self-Implementation Helpers

The authentication flow is abstracted for developer experience and ease of use, but you may want to customize certain aspects on a more fine-grained level. All the code that we use is open-source and exported via their own functions via the SDK and libraries.&#x20;

Below, we go a little deeper to show you how we perform all of our logic and potentially help you get started on a self-implementation.

BitBadges Quickstart

[Blockin Documentation](https://app.gitbook.com/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/)

* UI - Self-implementing signatures on your frontend? You could use the Blockin UI modal.
* Core Library Functions - Behind the scenes, the createChallenge, verifyChallenge and more is used to implement our flow.

BitBadges API

* Get Balances / Address Lists - Fetch balances / address lists manually to verify ownership requirements
* Verify Assets Generic - The generic verify assets route takes the Blockin verify and only punes it down to just check the assets portion.
* Create Auth Code - You can manually create an auth code (instead of storeInAccount = true). Requires authentication and correct scope though.

BitBadges SDK - Check out the following helper functions from the SDK

* **verifySignature** - Verifies a (message, signature) pair using any supported chain's verification algorithm
* verifySecretsProofs - Verifies the cryptographic side of secrets (signer is correct, etc) according to BitBadges format.

&#x20;SIgn In with Ethereum - Check out other libraries to generate challenge messages, such as SIWE.
