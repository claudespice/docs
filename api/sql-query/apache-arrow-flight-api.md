---
description: Query web3 data with SQL via the Apache Arrow Flight API
---

# Apache Arrow Flight API

SQL query results are now available as [Apache Arrow](https://arrow.apache.org) data frames via a high-performance [Apache Arrow Flight](https://arrow.apache.org/docs/format/Flight.html) endpoint.

Arrow Flight is a data protocol built on the high-performance, open-source [gRPC](https://grpc.io) protocol.

This enables high-speed access to your data in [Python](https://arrow.apache.org/docs/python/index.html), [Go](https://pkg.go.dev/github.com/apache/arrow/go/v8), [C++](https://arrow.apache.org/docs/cpp/index.html), [C#](https://github.com/apache/arrow/blob/master/csharp/README.md), and [Rust](https://docs.rs/arrow-flight/latest/arrow_flight/), and makes it easy to use libraries like [Pandas](https://arrow.apache.org/docs/python/pandas.html) and [NumPy](https://arrow.apache.org/docs/python/numpy.html?highlight=numpy).

We recommend using our SDKs to connect and query this endpoint. SDKs are available for [Python](../../sdks/python-sdk/), [Node.js](../../sdks/node.js-sdk/), and [Go](../../sdks/go.md) with more coming soon. In Python, the query results from the SDK can be easily converted to Pandas or NumPy format.

You may also use Apache's `pyarrow` library directly.

{% hint style="info" %}
**Note on Apple M1 Macs** - [How do I know if I have an M1?](https://support.apple.com/en-us/HT211814)

`The spicepy/pyarrow` installation requires [miniforge](https://github.com/conda-forge/miniforge).

See the [Python SDK page](../../sdks/python-sdk/#m1-macs) for installation steps.
{% endhint %}

#### Connecting to the endpoint

* Use the gRPC + TLS URL: `grpc+tls://flight.spiceai.io`
* For Firecache use the gRPC + TLS URL: `grpc+tls://firecache.spiceai.io`
  * For documentation on the Spice Firecache see [Broken link](broken-reference "mention")
* Use basic authentication
  * Username can be set to an empty string
  * Password should be set to the API key of your app

#### Requirements

* [Table](https://github.com/spicehq/cloud-docs/blob/trunk/api/sql-query-api/broken-reference/README.md) names must be fully-qualified. For example `eth.blocks`

#### Samples

Find code samples in Python in [Arrow Flight Samples](broken-reference).

### Troubleshooting

#### Mac/Windows Certificate issue

If you get this error:

`Could not get default pem root certs`

Install the [Let's Encrypt root certificates](https://letsencrypt.org/certificates/).

<details>

<summary><strong>Instructions for macOS</strong></summary>

First download the `roots.pem` file from the Let's Encrypt server:

```bash
curl -Lo isrgrootx1.pem https://letsencrypt.org/certs/isrgrootx1.pem 
```

Before running your code/jupyter notebook the environment variable `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` must be set to the pem file path. If you are using command from a terminal this can be done from the folder containing `isrgrootx1.pem` with:

```bash
export GRPC_DEFAULT_SSL_ROOTS_FILE_PATH="$PWD/isrgrootx1.pem"
```

The `export` command will set this variable for this specific terminal and thus will need to be run every time you open a new terminal. Additionally you can add to your terminal profile.

Note that `$PWD` is a bash-specific variable that will be replaced by the current directory path. You can download the certificate file `isrgrootx1.pem` in a specific location and inform this path instead of `$PWD`.

</details>

<details>

<summary>Instructions for Windows</summary>

```powershell
@powershell -NoProfile -ExecutionPolicy unrestricted -Command ^
    (new-object System.Net.WebClient).Downloadfile( ^
        'https://letsencrypt.org/certs/isrgrootx1.pem', 'isrgrootx1.pem')
set GRPC_DEFAULT_SSL_ROOTS_FILE_PATH=%cd%\isrgrootx1.pem
```

</details>
