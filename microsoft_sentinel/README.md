
## Create resources needed to stream logs to Azure Log Monitor and MS Sentinel

Note: all resources must be create in the same region.

1. Create Log Analytics workplace.
2. Create a new application in MS Entra (or use existing).
3. Create new application secret (copy the secret and secret ID)
4. Create Data Collection Endpoint (copy resource ID).

### Template
1. Create a new custom table(s) using Powershell command in [new_table_powershell.txt](new_table_powershell.txt)
2. Create a new DCR using [ARM template](dcr_template.json).
3. After deployment click on `Configure DCE` button and select DCE, created before.

### Access
1. Go to Access control (IAM) in DCR resource.
2. Click on `Add role assignment`.
3. Select `Monitoring Metrics Publisher` job function role.
4. Select the application, for which we created the secret.


To test it with CURL, get access token first. 

```
curl 'https://login.microsoftonline.com/{TENANT-ID-OF-THE-APP}/oauth2/v2.0/token' \
-H 'Content-Type: application/x-www-form-urlencoded' \
-d 'grant_type=client_credentials' \
-d 'client_id={CLIENT-ID}' \
-d 'client_secret={CLIENT-SECRET}' \
-d 'scope=https%3A%2F%2Fmonitor.azure.com%2F.default'```
``` 
Example of command to post logs:

```
curl '{LOG-INGESTION-ENDPOINT}/dataCollectionRules/{DCR-IMMUTABLE-ID}/streams/{DATASOURCE-NAME}?api-version=2023-01-01' \
-H 'Log-Type: MyCustomLog' \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer {access-key}' \
-d '[
    {
        "TimeGenerated": "2024-11-12T22:39:00.560Z",
        "company": "JFrog",
        "image": "fluentd",
        "instance": "partnership",
    },
    {
        "TimeGenerated": "2024-11-12T22:37:00.560Z",
        "company": "JFrog",
        "image": "fluentd",
        "instance": "partnership",
    }
]'
```

After posting logs the can be discovered in MS Sentinel. 