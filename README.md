# Sending Java Driver Metrics to Prometheus 
This application shows how to export metrics from DataStax Java driver 4.x to Prometheus via [Prometheus Java Client](https://github.com/prometheus/client_java).

Contributors: [Alex Ott](https://github.com/alexott)

## Objectives

* To demonstrate how to configure the DataStax Java Driver 4.x to report metrics
* To demonstrate how to configure the Prometheus client to push those metrics to Prometheus
  
## Project Layout
A list of key files within this repo and a short 1-2 sentence description of why they are important to the project

e.g.
* [MetricsWithPrometheus](/src/main/java/com/datastax/alexott/demos/MetricsWithPrometheus.java) - The main application file which contains all the logic to switch between the configurations

## How this Works
To configure the Prometheus Java Client you need to add the following dependencies to your pom.xml file.

```
    <dependency>
      <groupId>io.prometheus</groupId>
      <artifactId>simpleclient_dropwizard</artifactId>
      <version>${prometheus.version}</version>
    </dependency>
    <dependency>
      <groupId>io.prometheus</groupId>
      <artifactId>simpleclient_common</artifactId>
      <version>${prometheus.version}</version>
    </dependency>
    <dependency>
      <groupId>io.prometheus</groupId>
      <artifactId>simpleclient_hotspot</artifactId>
      <version>${prometheus.version}</version>
    </dependency>
    <dependency>
      <groupId>io.prometheus</groupId>
      <artifactId>simpleclient_httpserver</artifactId>
      <version>${prometheus.version}</version>
    </dependency>
```
Once that is added exporting of [Java driver metrics](https://docs.datastax.com/en/developer/java-driver/4.3/manual/core/metrics/) is straightforward.  
To begin exporting our metrics we just need to add following lines to our `Session` object after we :

```java
MetricRegistry registry = session.getMetrics()
        .orElseThrow(() -> new IllegalStateException("Metrics are disabled"))
        .getRegistry();
CollectorRegistry.defaultRegistry.register(new DropwizardExports(registry));
```

This then expose metrics to Prometheus by specific implementation - this example uses
Prometheus's `HTTPServer`, running on the port 9095 (overridable via `prometheusPort` Java
property).

## Setup and Running

### Prerequisites
The prerequisites required for this application to run are:

e.g.
* A DSE Cluster
* A Prometheus server (Install instruction are available [here](https://prometheus.io/docs/prometheus/latest/getting_started/))

### Running
Run example with following command:

```sh
mvn clean compile exec:java -Dexec.mainClass="com.datastax.alexott.demos.MetricsWithPrometheus" \
    -DcontactPoint=XX.XX.XX.XX -DdcName=dc1
```

You need to pass contact point & data center name as Java properties (`contactPoint` and
`dcName` correspondingly)

