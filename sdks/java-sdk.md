---
icon: java
---

# Java SDK

The [`Java SDK`](https://github.com/spiceai/spice-java) is the easiest way to query the Spice Cloud Platform from Java.

It uses [Apache Arrow Flight](https://arrow.apache.org/docs/format/Flight.html) to efficiently stream data to the client and [Apache Arrow](https://arrow.apache.org/) Records as data frames.

### Supported Java Versions

The library targets **Java 11** and above, and is tested against the following implementations:

* Microsoft OpenJDK 11, 17, 21
* Eclipse Temurin 21, 23, 24
* Oracle JDK 17, 21, 23, 24, 25

### Installation

{% tabs %}
{% tab title="Maven" %}
```xml
<dependency>
    <groupId>ai.spice</groupId>
    <artifactId>spiceai</artifactId>
    <version>0.6.0</version>
    <scope>compile</scope>
</dependency>
```
{% endtab %}

{% tab title="Gradle" %}
```groovy
implementation 'ai.spice:spiceai:0.6.0'
```
{% endtab %}
{% endtabs %}

### Usage

1\. Import the package.

```java
import ai.spice.SpiceClient;
```

2\. Create a `SpiceClient` by providing your API key. Get your free API key at [spice.ai](https://spice.ai/).

`SpiceClient` implements `AutoCloseable`, so use it in a try-with-resources block.

```java
try (SpiceClient spice = SpiceClient.builder()
        .withApiKey(ApiKey)
        .withSpiceCloud()
        .build()) {
    // query here
}
```

The builder also accepts `withFlightAddress(URI)`, `withHttpAddress(URI)`, `withUserAgent(String)`, `withMaxRetries(int)`, and `withArrowMemoryLimitMB(long)`. The API key must be in `appId|key` form.

3\. Execute a query and get back a [`FlightStream`](https://arrow.apache.org/docs/java/reference/org.apache.arrow.flight.core/org/apache/arrow/flight/FlightStream.html).

```java
FlightStream stream = spice.query("SELECT * FROM tpch.lineitem LIMIT 10");
```

4\. Iterate through the `FlightStream` to access the records.

```java
while (stream.next()) {
    try (VectorSchemaRoot batches = stream.getRoot()) {
        System.out.println(batches.contentToTSVString());
    }
}
```

Check [full example](https://github.com/spiceai/spice-java/blob/trunk/src/main/java/ai/spice/example/ExampleSpiceCloudPlatform.java) to learn more.

### Parameterized queries

`queryWithParams(String sql, Object... params)` binds positional `$1`, `$2` placeholders and returns an `ArrowReader`, which the caller closes.

```java
try (ArrowReader reader = spice.queryWithParams(
        "SELECT * FROM tpch.lineitem WHERE l_quantity > $1 LIMIT 10", 10)) {
    while (reader.loadNextBatch()) {
        System.out.println(reader.getVectorSchemaRoot().contentToTSVString());
    }
}
```

### Usage with local Spice.ai OSS runtime

Follow the [quickstart guide](https://github.com/spiceai/spiceai?tab=readme-ov-file#%EF%B8%8F-quickstart-local-machine) to install and run spice locally. The builder defaults to the local runtime:

```java
try (SpiceClient spice = SpiceClient.builder()
        .build()) {
    // query here
}
```

Or using custom flight address:

```java
try (SpiceClient spice = SpiceClient.builder()
        .withFlightAddress(new URI("grpc://my_remote_spice_instance:50051"))
        .build()) {
    // query here
}
```

{% hint style="warning" %}
Always include an explicit port. An `http://` or `https://` address is rewritten to a `grpc+tcp://` or `grpc+tls://` address using the URI's port, so an address without one resolves to an invalid port.
{% endhint %}

Check [Spice OSS documentation](https://docs.spiceai.org/sdks/java) or [Java SDK Sample](https://github.com/spiceai/samples/tree/trunk/client-sdk/spice-java-sdk-sample) to learn more

### Default endpoints

| Target         | Arrow Flight                     | HTTP                      |
| -------------- | -------------------------------- | ------------------------- |
| Spice.ai Cloud | `https://flight.spiceai.io:443`  | `https://data.spiceai.io` |
| Local runtime  | `http://localhost:50051`         | `http://localhost:8090`   |

These can also be set with the `SPICE_FLIGHT_URL` and `SPICE_HTTP_URL` environment variables.

### Refreshing a dataset

`refreshDataset(String dataset)` triggers a refresh of an accelerated dataset, optionally taking a `RefreshOptions`.

### Connection retry

The `SpiceClient` implements connection retry mechanism (3 attempts by default). The number of attempts can be configured with `withMaxRetries`:

```java
SpiceClient client = SpiceClient.builder()
    .withMaxRetries(5) // Setting to 0 will disable retries
    .build();
```

Retries are performed for connection and system internal errors. It is the SDK user's responsibility to properly handle other errors, for example `RESOURCE_EXHAUSTED (HTTP 429)`.

### Contributing

Contribute to or file an issue with the `spice-java` library at: [https://github.com/spiceai/spice-java](https://github.com/spiceai/spice-java)
