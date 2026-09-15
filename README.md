# OrigoID Python Library

[![fern shield](https://img.shields.io/badge/%F0%9F%8C%BF-Built%20with%20Fern-brightgreen)](https://buildwithfern.com?utm_source=github&utm_medium=github&utm_campaign=readme&utm_source=https%3A%2F%2Fgithub.com%2Forigoid%2Fsdk-python)
[![pypi](https://img.shields.io/pypi/v/origoid)](https://pypi.python.org/pypi/origoid)

Official SDK for OrigoID, a REST API for identity verification in Mexico.
Validate a CURP against RENAPO, check an INE voter credential against the
voter list (Lista Nominal), extract data from the INE card, validate RFC,
tax status certificates (CSF) and CFDI invoices at SAT, look up an NSS at
IMSS, screen against OFAC, PEPs and SAT 69 / 69-B lists, and compare faces.
Every response uses the same envelope, so integration is a switch on `type`.
Zero data retention: each request is validated, answered and discarded.

**En español** — SDK oficial de OrigoID para verificación de identidad en México:
validar CURP en RENAPO, vigencia de la credencial de elector en la Lista Nominal del
INE, OCR de INE, RFC, Constancia de Situación Fiscal y CFDI en el SAT, NSS en el IMSS,
búsqueda en OFAC, PEPs y listas SAT 69 y 69-B, y comparación facial. Cero retención
de datos. Guías: [Validar CURP](https://origoid.com/validar-curp.html) ·
[Validar INE / Lista Nominal](https://origoid.com/validar-ine.html) ·
[OCR de INE](https://origoid.com/ocr-ine.html) · [Validar RFC](https://origoid.com/validar-rfc.html) ·
[Comparación facial](https://origoid.com/comparacion-facial.html) ·
[Documentación](https://docs.origoid.com) · [Estatus](https://status.origoid.com)


## Table of Contents

- [Documentation](#documentation)
- [Installation](#installation)
- [Reference](#reference)
- [Usage](#usage)
- [Async Client](#async-client)
- [Exception Handling](#exception-handling)
- [Advanced](#advanced)
  - [Access Raw Response Data](#access-raw-response-data)
  - [Retries](#retries)
  - [Timeouts](#timeouts)
  - [Custom Client](#custom-client)
- [Contributing](#contributing)

## Documentation

API reference documentation is available [here](https://docs.origoid.com).

## Installation

```sh
pip install origoid
```

## Reference

A full reference for this library is available [here](https://github.com/origoid/sdk-python/blob/HEAD/./reference.md).

## Usage

Instantiate and use the client with the following:

```python
from origoid import OrigoID

client = OrigoID(
    api_key="<value>",
)

client.renapo.validate_curp(
    curp="TEST900101HDFRRN09",
)
```

## Async Client

The SDK also exports an `async` client so that you can make non-blocking calls to our API. Note that if you are constructing an Async httpx client class to pass into this client, use `httpx.AsyncClient()` instead of `httpx.Client()` (e.g. for the `httpx_client` parameter of this client).

```python
import asyncio

from origoid import AsyncOrigoID

client = AsyncOrigoID(
    api_key="<value>",
)


async def main() -> None:
    await client.renapo.validate_curp(
        curp="TEST900101HDFRRN09",
    )


asyncio.run(main())
```

## Exception Handling

When the API returns a non-success status code (4xx or 5xx response), a subclass of the following error
will be thrown.

```python
from origoid.core.api_error import ApiError

try:
    client.renapo.validate_curp(...)
except ApiError as e:
    print(e.status_code)
    print(e.body)
```

## Advanced

### Access Raw Response Data

The SDK provides access to raw response data, including headers, through the `.with_raw_response` property.
The `.with_raw_response` property returns a "raw" client that can be used to access the `.headers` and `.data` attributes.

```python
from origoid import OrigoID

client = OrigoID(...)
response = client.renapo.with_raw_response.validate_curp(...)
print(response.headers)  # access the response headers
print(response.status_code)  # access the response status code
print(response.data)  # access the underlying object
```

### Retries

The SDK is instrumented with automatic retries with exponential backoff. A request will be retried as long
as the request is deemed retryable and the number of retry attempts has not grown larger than the configured
retry limit (default: 2).

A request is deemed retryable when any of the following HTTP status codes is returned:

- [408](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/408) (Timeout)
- [429](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/429) (Too Many Requests)
- [5XX](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/500) (Internal Server Errors)

Use the `max_retries` request option to configure this behavior.

```python
client.renapo.validate_curp(..., request_options={
    "max_retries": 1
})
```

### Timeouts

The SDK defaults to a 60 second timeout. You can configure this with a timeout option at the client or request level.

```python
from origoid import OrigoID

client = OrigoID(..., timeout=20.0)

# Override timeout for a specific method
client.renapo.validate_curp(..., request_options={
    "timeout_in_seconds": 1
})
```

### Custom Client

You can override the `httpx` client to customize it for your use-case. Some common use-cases include support for proxies
and transports.

```python
import httpx
from origoid import OrigoID

client = OrigoID(
    ...,
    httpx_client=httpx.Client(
        proxy="http://my.test.proxy.example.com",
        transport=httpx.HTTPTransport(local_address="0.0.0.0"),
    ),
)
```

## Contributing

While we value open-source contributions to this SDK, this library is generated programmatically.
Additions made directly to this library would have to be moved over to our generation code,
otherwise they would be overwritten upon the next generated release. Feel free to open a PR as
a proof of concept, but know that we will not be able to merge it as-is. We suggest opening
an issue first to discuss with us!

On the other hand, contributions to the README are always very welcome!
