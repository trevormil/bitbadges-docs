# Socials / Email Plugins

**Plugin IDs: `twitter, github, discord, google, email, twitch`**

These plugins restrict the number of times specific users from different social platforms can use the claim.&#x20;

We also support assigning specific claim numbers if assignMethod === instance ID. We will use the zero-based index in the list o f\[...username,s ...ids]. Note we take first match so duplicates here are not supported.

**Usernames vs IDs**

We differentiate by account IDs (should be constant) and usernames for display purposes / ease of use.&#x20;

**Private Lists**

The user list is only allowed to be private. You may consider hosting the list on your end, so users know if they can claim or not.

### Public Parameters

* **maxUsesPerUser**: The maximum number of uses allowed per user.
* **blacklist:** Whether this should deny users on the IDs / usernames or not

### Private Parameters

If both are left blank, we do not check any whitelist restrictions.&#x20;

* **usernames**: An optional list of whitelisted usernames who are allowed to claim.
* **ids:** An optional list of whitelisted user IDs who are allowed to claim.

### State

State is maintained by tracking the number of claims each user makes. It keeps a record of user IDs and usernames.

Note that in order to maintain correctness, we replace all "." values with "\[dot]" to ensure the keysa re a single string. If micro-managing state through the API, you will need to do this.

```json
{
  "ids": {
    "user_id": 1
  },
  "usernames": {
    "username": "user_id"
  }
}
```

#### Default State

The default state of the plugin is defined as follows:

```json
{
  "ids": {},
  "usernames": {}
}
```

#### Public State

State is not made public to users.
