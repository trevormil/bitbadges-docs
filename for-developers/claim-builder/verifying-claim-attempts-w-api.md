# Verifying Claim Attempts w/ API

A big part of offering gated utility is to actually check if the user meets the criteria or not. While claims and BitBadges allows you to outsource all the heavy authentication logic to us, you still need to implement the connection logic and verify the attempt with the API if you are offering custom utility.

Verifying claim attempts are two-fold:

* Authentication: Verify the user owns the claiming address (can be done with Sign In with BitBadges)
* Verifying Claim Attempt: Lookup the claim attempt via the BitBadges API and cross-check the address satisfied criteria

