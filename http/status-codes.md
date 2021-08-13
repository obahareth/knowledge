# Status Codes

See [Mozilla's MDN documentation](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status) for a full list, I'm just going to cover the odd cases that have bitten me in the past here:

[HTTP 301](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/301) \(Moved Permanently\) and [302](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/302) \(Found, or a temporary redirect\) are supposed to leave the HTTP method unaltered \(based on this [RFC from 2014](https://tools.ietf.org/html/rfc7231#section-6.4.2)\), but many HTTP clients \(including curl\) do not respect that for historical reasons and change the method to a GET, as a result of that it's recommended to use [HTTP 308](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/308) \(Permanent Redirect\) and [307](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/307) \(Temporary Redirect\) if preserving the HTTP method is important.

