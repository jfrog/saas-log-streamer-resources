
> Azure Log streaming is work in progress and can't be enabled in MyJFrog UI at the moment.

## Create resources needed to stream logs to Azure Log Analytics. Logs can be used by MS Sentinel.

Note: **all resources must be created in the same region**.

1. Create new Resource group.
2. Create Log Analytics workspace, save resource ID for future reference.
3. Create MS Sentinel instance in the created workspace.
4. Create a new application in MS Entra (or use existing one).
5. Create new application secret (copy the secret and secret ID, it will be needed later).
6. Create Data Collection Endpoint (copy resource ID from JSON view, it will be needed later).

### Template

>Note: data types across the attributes in Custom Tables and in Data Collection rules should match.

1. Create a new custom table(s) using Powershell commands in [new_table_powershell.txt](assets/new_table_powershell.txt)
2. Create a new Data Collection Rules using [ARM template](assets/dcr_template.json) (use Deploy a Custom Template functionality). As a result one DCRs will be created with five `streamDeclarations` one per each log type. 
3. After deployment click on `Configure DCE` button and select Data Collection Endpoint, created before. It's not assigned by default.

### Access
1. Go to Access control (IAM) in Data Collection Rule resource.
2. Click on `Add` -> `Add role assignment`.
3. Select `Monitoring Metrics Publisher` job function role.
4. Select the application, for which we created the secret.


Paste Data Collection Endpoint URL into URL field (format `https://<workspace-name>.<region>.ingest.monitor.azure.com`)

Paste the following JSON into **Auth info** field:

* `Secret` - Azure application secret.
* `AzureTenantId` - Application Tenant ID 
* `AzureClientId` - Application Client ID.
* `AzureDataCollectionRule` - Name of Data Collection Rule, `Immutable ID` in format `dcr-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

Names of the streams are the same as set in the script, which created the custom tables. 

```
{
    "Secret": "<AzureClientSecret>",
    "AzureTenantId": "<AzureTenantId>",
    "AzureClientId": "<AzureClientId>",
    "AzureDataCollectionRule": "<dcr-xxxxx>",
    "AzureRequestLogStreamName": "Custom-ArtifactoryRequestLogRawData",
    "AzureAccessLogStreamName": "Custom-ArtifactoryAccessLogRawData",
    "AzureAccessAuditLogStreamName": "Custom-ArtifactoryAccessAuditLogRawData",
    "AzureAccessSecurityLogStreamName": "Custom-ArtifactoryAccessSecurityLogRawData",
    "AzureTrafficLogStreamName": "Custom-ArtifactoryTrafficLogRawData"
}
```

### Testing

Variables, needed to stream logs:
1. `${DCR_IMMUTABLE_ID}` - copy `Immutable ID` from the Data Collection rule. dcr-a2120123348548b4b38e991d7ce6bbe7
2. `${LOG_INGESTION_ENDPOINT}` - copy `Log ingestion` from Data Collection rules -> Data Collection Endpoint.
3. `${DATASOURCE_NAME}` - Data Collection Rules -> Data sources. Use the table names, URLs will be unique per log type.

To test log ingestion with CURL, get access token first. 

```
curl 'https://login.microsoftonline.com/{APPLICATION_TENANT_ID}/oauth2/v2.0/token' \
-H 'Content-Type: application/x-www-form-urlencoded' \
-d 'grant_type=client_credentials' \
-d 'client_id={CLIENT_ID}' \
-d 'client_secret={CLIENT_SECRET}' \
-d 'scope=https%3A%2F%2Fmonitor.azure.com%2F.default'```
``` 
Example of command to post logs (Artifactory Access log):

```
curl '${LOG_INGESTION_ENDPOINT}/dataCollectionRules/${DCR_IMMUTABLE_ID}/streams/{DATASOURCE_NAME}?api-version=2023-01-01' \
-H 'Log-Type: MyCustomLog' \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer {access-key}' \
-d '[
    {
        "TimeGenerated": "2024-11-14T03:20:22.755Z",
        "action": "LOGIN",
        "response": "ACCEPTED",
        "ip_address": "127.0.0.2",
        "log_timestamp": "2024-11-14T03:20:20.705Z",
        "message": "new test",
        "repository_path": "",
        "trace_id": "1cb671ed841eadf4",
        "user_name": "jfxr@01hx9f0nk0cf98q4rv0fe56h0h",
    },
    {
        "TimeGenerated": "2024-11-14T03:20:22.755Z",
        "action": "LOGIN",
        "response": "ACCEPTED",
        "ip_address": "127.0.0.2",
        "log_timestamp": "2024-11-14T03:20:20.705Z",
        "message": "new test",
        "repository_path": "",
        "trace_id": "1cb671ed841eadf4",
        "user_name": "jfxr@01hx9f0nk0cf98q4rv0fe56h0h",
    }
]'
```

After posting logs they can be discovered in MS Sentinel and in Log Analytics workspace.