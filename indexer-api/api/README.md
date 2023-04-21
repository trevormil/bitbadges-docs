# API

See [https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/api.ts](https://github.com/BitBadges/bitbadges-frontend/blob/main/src/bitbadges-api/api.ts) for an example implementation of all API routes.

### Getting Started

1. TODO API Key?
2. Start sending requests with the base URL of [https://api.bitbadges.io/](https://api.bitbadges.io/).

### Authentication

For certain requests, we require the user to be authenticated via [Blockin](http://localhost:5000/o/7VSYQvtb1QtdWFsEGoUn/s/AwjdYgEsUkK9cCca5DiU/). If the user is not signed in, the API will respond with a 401 error code. See [Authentication](authentication.md) for how to authenticate users.

### Error Handling

| Error Code | Description            |
| ---------- | ---------------------- |
| 500        | Internal server error. |
| 401        | Unauthorized request.  |

If an error occurs, the response body will be in the format of:

```json
{ 
  "error": "Error message" 
}
```

### Route Parameters

The following route parameters are used in the API:

* `:id`: Collection ID
* `:badgeId`: Badge ID
* `:accountNum`: Account ID
