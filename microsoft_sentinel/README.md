
> Azure Log streaming is work in progress and can't be enabled in MyJFrog UI at the moment.

## Create resources needed to stream logs to Azure Log Monitor. Logs can be used by MS Sentinel.

The project is a POC, for the scope of the project we only use Access and Request logs. 

Note: all resources must be created in the same region.

1. Create Log Analytics workspace.
2. Create MS Sentinel instance in the created workspace.
3. Create a new application in MS Entra (or use existing one).
4. Create new application secret (copy the secret and secret ID, it will be needed later).
5. Create Data Collection Endpoint (copy resource ID, it will be needed later).

### Template
1. Create a new custom table(s) using Powershell command in [new_table_powershell.txt](assets/new_table_powershell.txt)
2. Create a new Data Collection Rules using [ARM template](assets/dcr_template.json). As a result two DCRs will be created, one for Artifactory Access log, and one for Artifactory Request log. 
3. After deployment click on `Configure DCE` button in each DCR and select Data Collection Endpoint, created before.

### Access
1. Go to Access control (IAM) in Data Collection Rule resource.
2. Click on `Add role assignment`.
3. Select `Monitoring Metrics Publisher` job function role.
4. Select the application, for which we created the secret.


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
curl '{LOG_INGESTION_ENDPOINT}/dataCollectionRules/{DCR_IMMUTABLE_ID}/streams/{DATASOURCE_NAME}?api-version=2023-01-01' \
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

After posting logs they can be discovered in MS Sentinel. 