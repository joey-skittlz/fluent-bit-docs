---
description: An output plugin to submit Logs and Metrics to an OpenTelemetry endpoint
---

# OpenTelemetry

The OpenTelemetry plugin allows you to take logs and metrics from Fluent Bit and submit them to an OpenTelemetry HTTP endpoint.

Important Note: At the moment only HTTP endpoints are supported.

| Key                  | Description                                                  | Default   |
| -------------------- | ------------------------------------------------------------ | --------- |
| host                 | IP address or hostname of the target HTTP Server             | 127.0.0.1 |
| http_user            | Basic Auth Username                                          |           |
| http_passwd          | Basic Auth Password. Requires HTTP_user to be set            |           |
| port                 | TCP port of the target HTTP Server                           | 80        |
| proxy                | Specify an HTTP Proxy. The expected format of this value is `http://HOST:PORT`. Note that HTTPS is **not** currently supported. It is recommended not to set this and to configure the [HTTP proxy environment variables](https://docs.fluentbit.io/manual/administration/http-proxy) instead as they support both HTTP and HTTPS. |           |
| metrics_uri                  | Specify an optional HTTP URI for the target web server listening for metrics, e.g: /something | /         |
| logs_uri                  | Specify an optional HTTP URI for the target web server listening for logs, e.g: /something | /         |
| header               | Add a HTTP header key/value pair. Multiple headers can be set. |           |
| log_response_payload | Log the response payload within the Fluent Bit log           | false     |
| add_label            | This allows you to add custom labels to all metrics exposed through the OpenTelemetry exporter. You may have multiple of these fields |           |

## Getting Started

The OpenTelemetry plugin works with logs and only the metrics collected from one of the metric input plugins. In the following example, log records generated by the dummy plugin and the host metrics collected by the node exporter metrics plugin are exported by the OpenTelemetry output plugin. 

```
# Dummy Logs & Node Exporter Metrics with the OpenTelemetry output plugin
# -------------------------------------------
# The following example collects dummy logs and host metrics on Linux and delivers
# them through the OpenTelemetry plugin to a local collector :
#
[SERVICE]
    Flush                1
    Log_level            info

[INPUT]
    Name                 node_exporter_metrics
    Tag                  node_metrics
    Scrape_interval      2

[INPUT]
    Name                 dummy
    Tag                  dummy.log
    Rate                 3

[OUTPUT]
    Name                 opentelemetry
    Match                *
    Host                 localhost
    Port                 443
    Metrics_uri          /v1/metrics
    Logs_uri             /v1/logs
    Log_response_payload True
    Tls                  On
    Tls.verify           Off
    # add user-defined labels
    add_label            app fluent-bit
    add_label            color blue
```
