# Errors

> Errors across the API are structured like this:

```json
{
    "success": false,
    "errors": [
        {
            "message": "From airport must not be blank",
            "path": "from"
        }
    ]
}
```

> Where `path` is optional and may be omitted. That is, when a validation error occurs (for example, search parameters), path is provided for better context.

<aside class="notice">
Validation errors will be returned with a 400 Bad Request status code
</aside>

<aside class="notice">
Authentication errors will be returned with a 401 Unauthorized status code
</aside>
