## JFrog SaaS Log Streamer

The purpose of the application is to send logs from Artifactory SaaS instances to Log Management platforms of the customer's choice. 
Official [documentation](https://jfrog.com/help/r/jfrog-hosting-models-documentation/jfrog-cloud-log-streaming) on how to setup the log streaming to various log management platforms.
This documentation provides information on the attributes of each log type, which we are sending. 

Some of the attributes are renamed by the application before sending them to the Log Management platforms. This is done for compatibility with Log management platforms. 
Users shouldn't expect log attributes names to be identical with Self-hosted instance logs. 

[JFrog logs documentation](https://jfrog.com/help/r/jfrog-platform-administration-documentation/request-log).

### Artifactory Request Log

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


### Artifactory Access Log

* `action_response` - (Legacy) Action and response, concatenated by the parser to provide backward compatibility for older dashboard versions.
* `action` - Action type (e.g. DOWNLOAD, DEPLOY, LOGIN, CONFIGURATION_CHANGE etc.
* `response` - The response (ACCEPTED/DENIED).
* `ip_address` - The accessing user's IP address.
* `log_timestamp` - The date and time the message was logged, in UTC time with the standard format: `yyyy-MM-dd'T'HH:mm:ss.SSSZ` based on RFC-3339. If timestamp is missing, log message is dropped.
* `message` - An optional system message.
* `repository_path` - The repository that was accessed.
* `trace_id` - The trace id value. Trace id is used to identify a request across services,
* `user_name` - The accessing user's user name or "anonymous" when accessed anonymously.

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
