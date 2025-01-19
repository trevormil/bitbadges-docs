# API Limits & Restrictions

The following limits are in place to ensure API stability and performance. If these limits are too restrictive for your use case, please contact us.

## Request Limits

-   **Rate Limit**: 10,000 requests per day per API key
-   **Global Rate Limit**: Enforced to prevent infinite loops
-   **Refresh Rate**: Operations can only be refreshed once per hour

## Data Limits

-   **Batch Size**: Maximum 250 items per request for:
    -   Metadata URIs
    -   Account lookups
    -   Collection fetches
-   **IPFS Storage**: Maximum 100MB total uploads per address
-   **Collection Size**: Limited functionality for collections exceeding JavaScript's `Number.MAX_SAFE_INTEGER`

## Timeouts & Retries

This applies to any external fetches, including metadata URIs and other external sources like custom success hooks.

-   **URI Fetch Timeout**: 10 seconds maximum for direct source URI fetches
-   **Retry Policy**: For failed fetches, exponential backoff:
    ```
    Delay = 1 hour × 2^(number of attempts)
    ```

Note: These limits may change over time. Please refer to our latest documentation for current values.
