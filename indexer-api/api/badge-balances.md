# Badge Balances

### POST /api/v0/collection/:id/:badgeId/owners

Gets all owners for a specific badge (:badgeId) in a collection (:id).

#### Request

* Body: None

#### Response

* Status Code: 200
* Body:
*   ```json
    {
        balances: UserBalance[], //balances & approvals for each owner
        owners: number[] //account ID numbers
    }
    ```

    Returns the owners for a specific badge. Will be equal length (aka balances\[0] will be the balance for owners\[0]).

### POST /api/v0/collection/:id/balance/:accountNum

Fetches a user's (:accountNum) balance of badges for the collection (:id).

#### Request

* Body: None

#### Response

* Status Code: 200
* Body:
*   ```json
    {
        "badgeMetadata": UserBalance,
    }
    ```

    Returns the badge balances and approvals for a specific user.
