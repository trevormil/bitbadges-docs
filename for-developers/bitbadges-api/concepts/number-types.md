# Number Types

All requests / responses are stringified before being sent over HTTP. This is to avoid losing precision with big numbers > JavaScript's MAX\_SAFE\_INTEGER.  For how to convert the responses to your desired NumberType (bigint, JS number, etc), please see [Number Type Conversions](../../bitbadges-sdk/common-snippets/numbertype-conversions.md) from the SDK.

We recommend writing everything using the JavaScript bigint type.

Note: this is all handled for you if you use the BitBadges API SDK.
