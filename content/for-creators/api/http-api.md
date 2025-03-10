---
title: HTTP API
aliases:
  - /api/http-api
---

# HTTP API

Enables mods to make HTTP requests.

## Settings

To use this API, mods need to be listed in one or the other following
settings in CSV format:

- `secure.trusted_mods`
- `secure.http_mods`

An example would be:

```
secure.http_mods = modname1, modname2, modname3
```

## Types

### HttpRequest

A table consisting of the following parameters:

- `url` (required):
  - Type string
  - Example: `https://subdomain.domain.tld`
- `timeout` (optional):
  - Time in seconds
  - Default is what `curl_timeout` setting is set to (NOTE: setting is
    in milliseconds)
- `method` (optional):
  - Type string
  - Supports `GET`, `POST`, `PUT`, `DELETE`
  - Default is `GET`
- `data` (optional):
  - Supports string or table
  - Tables are encoded as `x-www-form-urlencoded` key value pairs
- `post_data` (optional):
  - Deprecated, see data above
- `user_agent` (optional):
  - Type string
  - Replaces the default user agent if provided
- `extra_headers` (optional):
  - Table of strings
  - Each string needs to follow http spec, `"key: value"`
- `multipart` (optional):
  - Boolean, default false
  - Method must be `POST`
  - Learn more at [MIME (Wikipedia)](https://en.wikipedia.org/wiki/MIME#Multipart_messages)

### HttpRequestResult

A table consisting of the following parameters

- `completed`:
  - Boolean
  - If request finished. By way of success, failure, or timing out.
- `succeeded`:
  - Boolean
  - Request status
- `timeout`:
  - Boolean
  - If it timed out or not, see HttpRequest timeout
- `code`:
  - Int
  - Http status code
- `data`:
  - Table or string
  - Response returned

## Functions

### core.request_http_api()

- Returns a table containing the following functions provided:
  - That the mod in question has been listed in one of the two
    settings above
  - The engine was built with curl support
  - `core.request_http_api()` is called from the mod's `init.lua` file and not in a function

{{< notice warning >}}
Store the result in a local variable, not a global, so that other mods cannot access it. Other mods should request their own access.
{{< /notice >}}

An example of how to use this API (handling both existence and permission
being granted) is as follows:

```lua
--inside mods init.lua file
local http = core.request_http_api and core.request_http_api()

if http then
    --call your http methods here
end
```

### HttpApi.fetch(request, callback)

Calls callback once completed. This method is recommended for the most
common use cases, see the next two methods for an alternate way

- Request is of type HttpRequest
- Callback receives one param of type `HttpRequestResult`

### HttpApi.fetch_async(request)

Returns a handle to be passed to `HttpApi.fetch_async_get`

- Request is of type HttpRequest

### HttpApi.fetch_async_get(handle)

Handle is provided by `HttpApi.fetch_async`, returns a HttpRequestResult
