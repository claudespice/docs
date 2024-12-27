# PostgreSQL Data Accelerator

To use PostgreSQL as Data Accelerator, specify `postgres` as the `engine` for acceleration.

```yaml
datasets:
  - from: spice.ai:path.to.my_dataset
    name: my_dataset
    acceleration:
      engine: postgres
```

### Configuration <a href="#configuration" id="configuration"></a>

The connection to PostgreSQL can be configured by providing the following `params`:

* `pg_host`: The hostname of the PostgreSQL server.
* `pg_port`: The port of the PostgreSQL server.
* `pg_db`: The name of the database to connect to.
* `pg_user`: The username to connect with.
* `pg_pass`: The password to connect with. Use the [secret replacement syntax](https://docs.spiceai.org/components/secret-stores) to load the password from a secret store, e.g. `${secrets:my_pg_pass}`.
* `pg_sslmode`: Optional. Specifies the SSL/TLS behavior for the connection, supported values:
  * `verify-full`: (default) This mode requires an SSL connection, a valid root certificate, and the server host name to match the one specified in the certificate.
  * `verify-ca`: This mode requires a TLS connection and a valid root certificate.
  * `require`: This mode requires a TLS connection.
  * `prefer`: This mode will try to establish a secure TLS connection if possible, but will connect insecurely if the server does not support TLS.
  * `disable`: This mode will not attempt to use a TLS connection, even if the server supports it.
* `pg_sslrootcert`: Optional parameter specifying the path to a custom PEM certificate that the connector will trust.
* `connection_pool_size`: Optional. The maximum number of connections to keep open in the connection pool. Default is 10.

Configuration `params` are provided either in the `acceleration` section of a dataset.

```yaml
datasets:
  - from: spice.ai:path.to.my_dataset
    name: my_dataset
    acceleration:
      engine: postgres
      params:
        pg_host: localhost
        pg_port: 5432
        pg_db: my_database
        pg_user: my_user
        pg_pass: ${secrets:my_pg_pass}
        pg_sslmode: require
```

### Arrow to PostgreSQL Type Mapping <a href="#arrow-to-postgresql-type-mapping" id="arrow-to-postgresql-type-mapping"></a>

The table below lists the supported [Apache Arrow data types](https://arrow.apache.org/rust/arrow/datatypes/enum.DataType.html) and their mappings to [PostgreSQL types](https://www.postgresql.org/docs/current/datatype.html) when stored

<table><thead><tr><th width="249">Arrow Type</th><th>sea_query ColumnType</th><th>PostgreSQL Type</th></tr></thead><tbody><tr><td><code>Int8</code></td><td><code>TinyInteger</code></td><td><code>smallint</code></td></tr><tr><td><code>Int16</code></td><td><code>SmallInteger</code></td><td><code>smallint</code></td></tr><tr><td><code>Int32</code></td><td><code>Integer</code></td><td><code>integer</code></td></tr><tr><td><code>Int64</code></td><td><code>BigInteger</code></td><td><code>bigint</code></td></tr><tr><td><code>UInt8</code></td><td><code>TinyUnsigned</code></td><td><code>smallint</code></td></tr><tr><td><code>UInt16</code></td><td><code>SmallUnsigned</code></td><td><code>smallint</code></td></tr><tr><td><code>UInt32</code></td><td><code>Unsigned</code></td><td><code>bigint</code></td></tr><tr><td><code>UInt64</code></td><td><code>BigUnsigned</code></td><td><code>numeric</code></td></tr><tr><td><code>Decimal128</code> / <code>Decimal256</code></td><td><code>Decimal</code></td><td><code>decimal</code></td></tr><tr><td><code>Float32</code></td><td><code>Float</code></td><td><code>real</code></td></tr><tr><td><code>Float64</code></td><td><code>Double</code></td><td><code>double precision</code></td></tr><tr><td><code>Utf8 / LargeUtf8</code></td><td><code>Text</code></td><td><code>text</code></td></tr><tr><td><code>Boolean</code></td><td><code>Boolean</code></td><td><code>bool</code></td></tr><tr><td><code>Binary / LargeBinary</code></td><td><code>VarBinary</code></td><td><code>bytea</code></td></tr><tr><td><code>FixedSizeBinary</code></td><td><code>Binary</code></td><td><code>bytea</code></td></tr><tr><td><code>Timestamp</code> (no Timezone)</td><td><code>Timestamp</code></td><td><code>timestamp</code> without time zone</td></tr><tr><td><code>Timestamp</code> (with Timezone)</td><td><code>TimestampWithTimeZone</code></td><td><code>timestamp</code> with time zone</td></tr><tr><td><code>Date32</code> / <code>Date64</code></td><td><code>Date</code></td><td><code>date</code></td></tr><tr><td><code>Time32</code> / <code>Time64</code></td><td><code>Time</code></td><td><code>time</code></td></tr><tr><td><code>Interval</code></td><td><code>Interval</code></td><td><code>interval</code></td></tr><tr><td><code>Duration</code></td><td><code>BigInteger</code></td><td><code>bigint</code></td></tr><tr><td><code>List</code> / <code>LargeList</code> / <code>FixedSizeList</code></td><td><code>Array</code></td><td><code>array</code></td></tr><tr><td><code>Struct</code></td><td><code>N/A</code></td><td><code>Composite</code> (Custom type)</td></tr></tbody></table>

{% hint style="warning" %}
**LIMITATIONS**

* The Postgres federated queries may result in unexpected result types due to the difference in DataFusion and Postgres size increase rules. Please explicitly specify the expected output type of aggregation functions when writing query involving Postgres table in Spice. For example, rewrite `SUM(int_col)` into `CAST (SUM(int_col) as BIGINT`.
{% endhint %}
