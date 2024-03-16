# API Calls

The claim builder supports custom API calls via the API plugin. We will call the specified URI via a POST HTTP request with the following body (Discord and Twitter are only passed if passDiscord and passTwitter is true in the configs).

```
{
    claimId,
    cosmosAddress,
    discord: {
        username: req.session.discord?.username,
        id: req.session.discord?.id,
        discriminator: req.session.discord?.discriminator
    },
    twitter: {
        username: req.session.twitter?.username,
        id: req.session.twitter?.id
    }
}
```

To pass the validation check, a successful response must be received (e.g. status code 200).

Couple notes:

* You should not depend on any state. This can cause data race conditions, especially if another plugin fails.
* You are responsible for making sure the endpoint is accessible (e.g. no CORS errors, etc.).&#x20;
