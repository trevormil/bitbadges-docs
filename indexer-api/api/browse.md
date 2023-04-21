# Browse

### POST /api/v0/browse

Fetches the collections that are featured, recently created, most popular, etc.

#### Request

* Body: None

#### Response

* Status Code: 200
* Body:
* ```json
  {
    'featured': StoredBadgeCollection[],
    'latest': StoredBadgeCollection[],
    'claimable': StoredBadgeCollection[],
    'popular': StoredBadgeCollection[],
  }
  ```

