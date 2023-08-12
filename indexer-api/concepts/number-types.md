# Number Types

All requests / responses are stringified before being sent over HTTP. This is to handle big numbers > JavaScript's MAX\_SAFE\_INTEGER.

For how to convert the responses to your desired NumberType (bigint, JS number, etc), please see [Number Type Conversions](../../sdk/common-snippets/numbertype-conversions.md).
