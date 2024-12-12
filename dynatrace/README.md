## Set up Cloud Log Streaming with Dynatrace

Perform the following steps to set up log streaming with Dynatrace.

1. In Dynatrace, go to **Access tokens** menu.

![dynatrace_tokens.png](assets/dynatrace_tokens.png) 

  Generate a new token with Ingest logs (`logs.ingest`) permissions.

2. Go to the [MyJFrog Portal](http://my.jfrog.com/).

3. Additionally, you can access the MyJFrog Portal from the JFrog Platform. For more information, see [Platform Single Sign-On to MyJFrog](https://jfrog.com/help/r/5H19DEVA7PsahAXH0xXNSg/_iPFuW3rDQk_mlAk9URBkQ).

> Note: You must be a Platform Admin to access the MyJFrog Portal via the JFrog Platform.

Log into the JFrog Platform, and in the left navigation bar of the **Application** module, click **MyJFrog Portal**.
This opens the **MyJFrog Portal** in a new tab in your browser.

4. Select **Settings** from the left navigation menu.

5. Select the **JFrog Cloud Log Streaming** tab.

6. Turn on the **Log Streaming** toggle.

7. Select **Dynatrace**.

![dynatrace.png](assets/dynatrace.png)

8. Enter the **Dynatrace API key** and log ingestion URL.

9. Click **Save**.

The example dashboard in JSON format can be found in the `dashboards` folder.