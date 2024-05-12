# Automatic Claim Tutorial

### Create the Claim

The first step is to create the claim via the Bitbadges site; however, note that the configuration of the claim must be correct to ensure correct behavior of the claim process and allow Zapier to communicate.

**No Signed In with BitBadges Plugin**

&#x20;This should be disabled to allow Zapier to claim on behalf of others without needing to authenticate. In other words, whoever is claiming can specify the intended recipient without actually needing to authenticate as this recipient.

**Gate with Custom Password**

However, it is IMPORTANT to note that not requiring SIWBB relaxes the restriction for everyone. Thus, you will have to gate the claim in other ways to ensure that it only allows Zapier to claim on users' behalf. The easiest way to do this is with a unique password. You will then configure Zapier to use this password whenever it tries to claim. This allows claims from Zapier to pass but claims from anyone else not to (because noone else knows the password).

**Other Plugins**

You may still configure other criteria to be checked as well (e.g. one use per recipient address, check min $BADGE, etc.) However, anything that requires user inputs at claim time will be tricky or impossible to implement within a Zap.

For example, to check an address' GitHub contributions, they need to Sign In with GitHub, but the Zap cannot sign in the user with GitHub. Thus, this plugin is not usable. However, certain ones like checking badge ownership or checking minimum $BADGE owned do not require any user inputs and will be fine to use.

Note that you are also integrating with Zapier, so you can also chain anyone of the 6000+ app integrations into your automated Zap. Oftentimes, you can find a plugin that does exactly what the one on BitBadges would do.

## Tutorial

TODO
