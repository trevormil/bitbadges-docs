# API Calls

The claim builder supports custom API calls via the API plugin. We will call the specified URI via a POST HTTP request with the following body

```
{
    claimId,
    cosmosAddress
}
```

To pass the validation check, a successful response must be received (e.g. status code 200).
