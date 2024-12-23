## 1.7.1 (December 17, 2024)

IMPROVEMENTS:

* Added attribute `trace_id` to Artifactory request log. This allows to correctly track traces in Datadog. The existing attribute `traceid` is still present in the log but will be removed in Q1 2025. 

## 1.7.0 (December 12, 2024)

IMPROVEMENTS:

* Added backend support for streaming logs to Azure Log Analytics to provide data for MS Sentinel. Streaming is not available in MyJFrog as for now.
* Minor fixes.

## 1.6.9 (December 3, 2024)

IMPROVEMENTS:

* Move Splunk attribute `sourcetype` from log message to payload metadata section. The attribute value is a log type in a following format - `jfrog.saas.rt.<service-name>.<log-type>`.
