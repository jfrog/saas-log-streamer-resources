## 1.9.2 (April 1, 2025)

* Azure Log Monitor support is added to MyJFrog UI.

## 1.9.1 (January 28, 2025)

IMPROVEMENTS:

* Added attribute `group` to Artifactory request log. Possible values are `download` and `probes`.

## 1.9.0 (January 14, 2025)

IMPROVEMENTS:

* Added streaming of a new log type `artifactory-request-out`.

## 1.8.0 (January 9, 2025)

IMPROVEMENTS:

* Added functionality to get tenant information from a new admin service as well as from MyJFrog.

## 1.7.2 (December 20, 2024)

IMPROVEMENTS:

* Backend changes in Azure log parser.
* Upgrade Go to 1.23.4

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
