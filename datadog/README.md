## Set up Cloud Log Streaming with Datadog

Perform the following steps to set up log streaming with Datadog.

1. Fetch **Datadog API key** from the [Organization Settings](https://app.datadoghq.com/organization-settings/api-keys) page in Datadog.
For more information, refer to the [Datadog documentation](https://docs.datadoghq.com/account_management/api-app-keys/).

2. Identify your **Datadog URL** based on the location as listed in the [table](https://docs.datadoghq.com/getting_started/site/#access-the-datadog-site) in Datadog documentation.

3. Form a key-value pair of any additional parameters that you need to add to the logs in the following format:
```
key1:value1,key2:value2
```
For example, `env:staging,version:5.1.`

4. Go to the [MyJFrog Portal](http://my.jfrog.com/).
5. Additionally, you can access the MyJFrog Portal from the JFrog Platform. For more information, see [Platform Single Sign-On to MyJFrog](https://jfrog.com/help/r/5H19DEVA7PsahAXH0xXNSg/_iPFuW3rDQk_mlAk9URBkQ).

> Note: You must be a Platform Admin to access the MyJFrog Portal via the JFrog Platform.

Log into the JFrog Platform, and in the left navigation bar of the **Application** module, click **MyJFrog Portal**.
This opens the **MyJFrog Portal** in a new tab in your browser.

6. Select **Settings** from the left navigation menu.

7. Select the **JFrog Cloud Log Streaming** tab.

8. Turn on the **Log Streaming** toggle.

> Note: If your account has the multi-JPD (**Per JPD**) feature, the **Select your log streaming mode** section appears below the toggle. It shows **All JPDs** and **Per JPD**.
> If your account does not have this feature, the vendor list appears directly below the toggle. In this case, follow **Option A** and skip its step 1.

9. Follow **Option A** or **Option B**.

* Use **Option A: All JPDs** to send logs from every JPD to 1 destination.
* Use **Option B: Per JPD** to give each JPD its own destination.

#### Option A: All JPDs

1. Select **All JPDs**.

2. Select **Datadog**.

![datadog.png](assets/datadog.png)

3. Enter the **Datadog API key** and select **Datadog URL** from the dropdown list.

4. Enter the key-value pair that you formed in the **Datadog Tags** field.

5. Click **Save**.

#### Option B: Per JPD

1. Select **Per JPD**.

![datadog_per_jpd.png](assets/datadog_per_jpd.png)

2. Expand the row of the JPD that you want to configure.

3. Select **Datadog**.

4. Enter the **Datadog API key**, **Datadog URL**, and **Datadog Tags** for the destination of this JPD.

5. Repeat steps 2–4 for each JPD that you want to configure. JPDs that you leave empty stay **Not configured**.

6. Click **Save All**.

Each JPD sends logs to 1 destination only. Different JPDs can use different vendors.

> Note: When you switch from **All JPDs** to **Per JPD**, each JPD without its own settings starts with the **All JPDs** vendor and settings. Check each JPD before you click **Save All**.

> Note: When you switch from **Per JPD** to **All JPDs**, the **Switch to All JPDs?** dialog appears if JPDs have their own settings. If you click **Discard & switch**, MyJFrog discards the per-JPD settings. You cannot recover them.

The example dashboard will be installed together with the [JFrog Cloud integration](https://docs.datadoghq.com/integrations/jfrog_platform_cloud/), which can be found in the Integrations menu in Datadog.