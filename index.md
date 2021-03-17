# Divvy webhook integration guide

## Endpoint

`POST /api/v1/webhook`

Send product menu data in the request body as a JSON object.

### Required headers

| Header                              | Description                                                                                                                                                  |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Authorization: Token {access key}` | Access key provided by Divvy to each integrator to authenticate requests to our service. The same access key should be used regardless of which client's data is being provided. |
| `X-Webhook-Token: {client key}`     | Key provided by Divvy to identify which client this data is for.                                                                                        |

### Response structure

Returns a JSON-encoded response with the following fields. Successful responses return HTTP status 200, while errors use 400 or greater.

| Name              | Type      | Description                                                                                                                                            |
| ----------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `success`         | `boolean` | `true` if the client's data was received and no errors occurred, `false` otherwise. If `false`, `error` will contain a description of what went wrong. |
| `error`           | `string`  | A description of the error that occurred, if any.                                                                                                      |
| `last_used`       | `string`  | Time this client's data was last used by our system. ISO 8601 formatted datetime string.                                                               |
| `next_push_after` | `string`  | Time after which our system expects the next update for this client. ISO 8601 formatted datetime string.                                               |

### Error messages

| HTTP Response Code | Message                                               | Explanation                                                                                                                                                                        |
| ------------------ | ----------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 400                | `next push not allowed until after <datetime>`        | Returned when this endpoint is called again before `next_push_after` from the previous call for this client. `<date-time>` will be the `next_push_after` ISO 8601 datetime string. |
| 400                | `must supply webhook token in X-Webhook-Token header` | Returned when the request is missing the client key in the `X-Webhook-Token` header.                                                                                               |
| 403                | `not authorized`                                      | Returned when the request is missing the access key in the `Authorization` header.                                                                                                 |
