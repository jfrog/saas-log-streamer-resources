## JFrog SaaS Log Streamer

### Supported log streaming destinations

* [Datadog](datadog/README.md)
* [Dataset](dataset/README.md)
* [Dynatrace](dynatrace/README.md)
* [Elastic](elastic/README.md)
* [Grafana Loki](grafana_loki/README.md)
* [New Relic](new_relic/README.md)
* [Splunk](splunk/README.md)


The purpose of the application is to send logs from Artifactory SaaS instances to Log Management platforms of the customer's choice. 
Official documentation on how to setup the log streaming to various log management platforms can be found [here](https://jfrog.com/help/r/jfrog-hosting-models-documentation/jfrog-cloud-log-streaming).
This documentation provides information on the attributes of each log type, which we are sending. 

Some of the attributes are renamed by the application before sending them to the Log Management platforms. This is done to assure compatibility with various Log Management platforms. 
Users shouldn't expect log attributes names to be identical with Self-hosted Artifactory instance logs. 

[JFrog logs documentation](https://jfrog.com/help/r/jfrog-platform-administration-documentation/request-log).

## Whitelisting

Logs are streaming directly from the Artifactory instances to the customers' log management platform. To be able to stream logs to the log management platforms behind firewalls, please make sure to whitelist your Artifactory regions. 
See the [official documentation](https://jfrog.com/help/r/what-are-artifactory-cloud-nated-ips/what-are-artifactory-cloud-nated-ips) for the reference. 

Also, whitelist the following CIDR ranges for the management region:

```
52.206.165.145/32
3.218.212.121/32
3.95.51.114/32
3.216.224.216/32
```

Whitelisting of these CIDR ranges is necessary, because MyJFrog sends a validation call to the customer's log management platform from the separate region. 


## Payload attributes description

Payload description for the call from Log Dispatcher to the log management platform (DataDog, Splunk, etc.).
Usually, log management platforms use the same pattern of the payload, which consists of metadata and actual log message. We are formatting the payload according to the official log management platform documentation for HTTP data injection.

### Datadog metadata

* `ddsource` - (Required) Data source, constant, set to `jfrog_artifactory` for SaaS Log Streamer.
* `ddtags` - (Optional) List of tags assigned to the payload. Assigned on MyJFrog platform by the customer along with DataDog token.
* `hostname` - (Required) Hostname, constant, set to `jfrog-cloud` for SaaS Log Streamer.
* `service` - (Required) The name of the service, which generated the log entry. Set to `jfrog.saas.rt.<log_name>`.
* `tenant_id` - (Required) Technical server name (k8s namespace name). Used to sort logs and assign them to the proper destination info by Log Dispatcher. This value originates form JPMS. Most of the `tenant_id` are auto-generated, but some of them are the same as instance names.
* `instance_name` - (Optional) Instance name in JPMS. Used in the Coralogix reporting.
* `company_name` - (Optional) Company name in JPMS. Used in the Coralogix reporting.
* `message` - (Required) Actual log message.

### Splunk metadata

* `time` - (Required) Timestamp in nano format (1711565731515000000).
* `host` - (Required) Hostname, constant, set to `jfrog-cloud` for SaaS Log Streamer.
* `sourcetype` - (Required) The name of the service, which generated the log entry. Set to `jfrog.saas.rt.<log_name>`.
* `event` - (Required) Actual log message. Splunk event contains several attributes that we include in DataDog metadata, such as: `company`, `instance`, `service` and `tenant_id`.

### Dataset metadata

* `serverType` - (Optional) Hostname, constant, set to `jfrog-cloud` for SaaS Log Streamer.
* `serverId` - (Optional) Technical server name (k8s namespace name).
* `instanceName` - (Optional) Instance name in JPMS. The same as used in the Coralogix reporting.
* `company_name` - (Optional) Company name in JPMS. The same as used in the Coralogix reporting.

### Dynatrace metadata

* `log.source` - (Optional) Constant, set to `jfrog_artifactory`.
* `host.name` - (Optional) Constant, set to `jfrog-cloud`.
* `service.name` - (Optional) The name of the service, which generated the log entry. Set to `jfrog.saas.rt.<log_name>`.
* `host.id` - (Optional) Instance name in JPMS. The same as used in the Coralogix reporting.
* `host.hostname` - (Optional) Instance name in JPMS. The same as used in the Coralogix reporting.
* `company.name` - (Optional) Company name in JPMS. The same as used in the Coralogix reporting.
* `timestamp` - (Optional) Event timestamp in format `2006-01-02T15:04:05.000Z`,
* `content` - (Optional) Actual log message.

### New Relic metadata

* `hostname` - (Optional) Constant, set to `jfrog-cloud`.
* `tenant_id` - Technical server name (k8s namespace name). Used to sort logs and assign them to the proper destination info by Log Dispatcher. This value originates form JPMS. Most of the `tenant_id` are auto-generated, but some of them are the same as instance names.
* `instance_name` - (Optional) Instance name in JPMS. The same as used in the Coralogix reporting.
* `company_name` - (Optional) Company name in JPMS. The same as used in the Coralogix reporting.

### Loki metadata

* `service_name` - (Optional, top level "stream") Constant, set to `jfrog-cloud`.

Loki only indexes the metadata attributes by design. Metadata is attached to each log record:

* `tenant_id` - (Optional) Technical server name (k8s namespace name). Used to sort logs and assign them to the proper destination info by Log Dispatcher. This value originates form JPMS. Most of the `tenant_id` are auto-generated, but some of them are the same as instance names.
* `instance_name` - (Optional) Instance name in JPMS. The same as used in the Coralogix reporting.
* `company_name` - (Optional) Company name in JPMS. The same as used in the Coralogix reporting.
* `service` - (Optional) The name of the service, which generated the log entry. Set to `jfrog.saas.rt.<log_name>`.

Log by itself is not indexed and stored as a plain text.

### Elastic metadata

* `_index` - (Required) The index where the logs will be pushed. Set to `jfrog_cloud` by default, it will be customizable in MyJFrog UI in future releases.

Elastic doesn't have any other metadata and we push logs as-is into the index using [bulk API](https://www.elastic.co/guide/en/serverless/current/elasticsearch-ingest-data-through-api.html#elasticsearch-ingest-data-through-api-using-the-bulk-api).


### Artifactory Request Log

[Official documentation](https://jfrog.com/help/r/jfrog-platform-administration-documentation/request-log)

* `image` -  Name of docker image. This attribute is only assigned if the request is related to docker pull/push event.
* `log_timestamp` - The date and time the message was logged, in UTC time with the standard format: `yyyy-MM-dd'T'HH:mm:ss.SSSZ` based on RFC-3339. If timestamp is missing, log message is dropped (except Dynatrace).
* `remote_address` - (Deprecated) The IP address of the remote caller (ipv4 or ipv6). Will be removed in the next major version.
* `ip_address` -  The IP address of the remote caller (ipv4 or ipv6).
* `repo` -  Name of docker repository. This attribute is only assigned if the request is related to docker pull/push event.
* `request_content_length` -  The size of the user request in bytes, for example, the size of an uploaded file. -1 if unknown.
* `request_duration` -  The time in ms for the request to process.
* `request_method` -  The HTTP request method, in UPPERCASE.
* `request_url` -  The relative URL for the request.
* `request_user_agent` -  The request user agent.
* `response_content_length` -  The size of the server response in bytes, for example, the size of downloaded file. -1 if unknown (for example, chunked encoding).
* `return_status` -  The HTTP return code for the request.
* `trace_id` -  The trace id value.
* `traceid` - (Deprecated) The trace id value, copy of `trace_id`, will be removed in the future releases.
* `user_name` -  The requesting user's username or "anonymous" when accessed anonymously.

Payload example:

```
{
    "image": "fluentd",
    "log_timestamp": "2024-05-11T00:39:00.560Z",
    "remote_address": "127.0.0.1",
    "ip_address": "127.0.0.1",
    "repo": "docker-trial",
    "request_content_length": -1,
    "request_duration": 1,
    "request_method": "GET",
    "request_url": "/api/docker/docker-trial/v2/fluentd/manifests/sha256:91a25ac8c428531f6e0992f4fa115a683d434b8b09c051b2a934ecad5df92375",
    "request_user_agent": "curl/7.76.1",
    "response_content_length": 0,
    "return_status": "200",
    "trace_id": "fb6ea090c75631aa",
    "user_name": "non_authenticated_user"
}
```

### Artifactory Request Out Log (Outbound Request Log)

[Official documentation](https://jfrog.com/help/r/jfrog-platform-administration-documentation/outbound-request-log)

* `user_name` - The requesting user's username or "anonymous" when accessed anonymously.
* `request_method` - The HTTP request method, in UPPERCASE.
* `request_url` - The URL for the remote resource.
* `request_content_length` - The size of the user request in bytes, for example, the size of an uploaded file. -1 if unknown.
* `remote_repository` - The name of the remote repository.
* `trace_id` - The trace id value.
* `response_content_length` - The size of the server response in bytes, for example, the size of downloaded file. -1 if unknown (for example, chunked encoding).
* `return_status` - The HTTP return code for the request.
* `log_timestamp` - (Required)  The date and time the message was logged, in UTC time with the standard format: [yyyy-MM-dd'T'HH:mm:ss.SSSZ] based on RFC-3339. If timestamp is missing, log message is dropped (except Dynatrace).
* `request_duration` - The time in ms for the request to process.
* `package` - Package type.
* `group` - Type of action.

Payload example: 

```
{
    "log_timestamp": "2025-01-13T14:29:15.414Z",
    "package": "nuget",
    "group": "download",
    "remote_repository": "nuget-remote",
    "request_content_length": 0,
    "request_duration": 44,
    "request_method": "HEAD",
    "request_url": "https://api.nuget.org/v3/registration5-gz-semver2/microsoft.net.test.sdk/index.json",
    "response_content_length": 667,
    "return_status": "200",
    "trace_id": "97350f7acf494a29",
    "user_name": ""
}
```

### Artifactory Access Log

[Official documentation](https://jfrog.com/help/r/jfrog-platform-administration-documentation/access-log)

* `action_response` - (Legacy) Action and response, concatenated by the parser to provide backward compatibility for older dashboard versions.
* `action` - Action type (e.g. DOWNLOAD, DEPLOY, LOGIN, CONFIGURATION_CHANGE etc.
* `response` - The response (ACCEPTED/DENIED).
* `ip_address` - The accessing user's IP address.
* `log_timestamp` - The date and time the message was logged, in UTC time with the standard format: `yyyy-MM-dd'T'HH:mm:ss.SSSZ` based on RFC-3339. If timestamp is missing, log message is dropped.
* `message` - An optional system message.
* `repository_path` - The repository that was accessed.
* `trace_id` - The trace id value. Trace id is used to identify a request across services,
* `user_name` - The accessing user's username or "anonymous" when accessed anonymously.

Payload example:

```
{
    "action_response": "ACCEPTED LOGIN",
    "action": "LOGIN",
    "response": "ACCEPTED",
    "ip_address": "127.0.0.1",
    "log_timestamp": "2024-05-09T21:20:20.700Z",
    "message": "",
    "repository_path": "",
    "trace_id": "1cb671ed841eadf4",
    "user_name": "jfxr@01hx9f0nk0cf98q4rv0fe56h0h"
}
```

### Access Audit Log

* `event` - Event name (created, deleted, etc.)
* `expirationtime` - Token expiration time.
* `issuer` - Entity, that issued the token.
* `log_timestamp` - The date and time the message was logged, in UTC time with the standard format: `yyyy-MM-dd'T'HH:mm:ss.SSSZ` based on RFC-3339. If timestamp is missing, log message is dropped.
* `refreshable` - Wether the token is refreshable or not.
* `subject` - The entity to which token was issued.
* `token_id` - Token ID.
* `user_name` - Performing username.

Payload example:

```
{
    "event": "created",
    "expirationtime": "1706739134223",
    "issuer": "jfob@01h90ampkvn2wy0e10sgyx174d",
    "log_timestamp": "2024-02-08T22:21:11.665Z",
    "refreshable": false,
    "subject": "jfob@01h90ampkvn2wy0e10sgyx174d",
    "token_id": "0634d4e8-5338-4f65-9310-ddfe4f4e8401",
    "user_name": "jfob@01h90ampkvn2wy0e10sgyx174d"
}
```


### Access Security Audit Log

[Official documentation](https://jfrog.com/help/r/jfrog-platform-administration-documentation/audit-trail-log)

* `data_changed` - A JSON describing the data that was changed. The following describes a map that specifies permissions when creating or updating a permission target: r = Read, t = Annotate, w = Deploy/Cache, d = Delete/Overwrite, m = Manage.
* `entity_name` - The security entity that the operation modified. For example, permission target name, group name, username etc.
* `event` - The security entity on which the operation was performed where: USR = user, GRP = Group, PRM = Permission, TKN = Token.
* `event_type` - The type of operation performed where: C = Create, U = Update, D = Delete.
* `ip_address` - The IP address of the user that performed the operation in Artifactory. Note: The IP address is shown as `unknown` if the operation was performed with an internal service token, which is used only for internal communication.
* `log_timestamp` - The date and time the message was logged, in UTC time with the standard format: `yyyy-MM-dd'T'HH:mm:ss.SSSZ` based on RFC-3339. If timestamp is missing, log message is dropped.
* `logged_principal` - The login information of the Artifactory service that performed the operation against Access.
* `trace_id` - The trace id value. Trace id is used to identify a request across services.
* `user_name` - The username of the user that performed the operation in Artifactory. Note: The username is shown as `unknown` if the operation was performed with an internal service token, which is used only for internal communication.


Payload example:

```
{
    "data_changed":
    {
        "added":
        {
            "created": "1706739014223",
            "expirationTime": "1706739134223",
            "id": "0634d4e8-5338-4f65-9310-ddfe4f4e8401",
            "owner": "jfob@01h90ampkvn2wy0e10sgyx174d",
            "scope": "api:* applied-permissions/admin",
            "subject": "jfob@01h90ampkvn2wy0e10sgyx174d",
            "type": "generic"
        }
    },
    "entity_name": "jfob@01h90ampkvn2wy0e10sgyx174d",
    "event": "TKN",
    "event_type": "C",
    "ip_address": "UNKNOWN",
    "log_timestamp": "2024-02-06T22:15:11.665Z",
    "logged_principal": "jfob@01h90ampkvn2wy0e10sgyx174d",
    "trace_id": "6420b6d27625d991",
    "user_name": "UNKNOWN"
}
 ```
