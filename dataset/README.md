## Set up Cloud Log Streaming with Dataset

Perform the following steps to set up log streaming with Dataset.

1. In Dataset, click on your username on the top right of the screen, select the API Keys menu, and add a new key with Write access.

2. Go to the [MyJFrog Portal](http://my.jfrog.com/).

3. Additionally, you can access the MyJFrog Portal from the JFrog Platform. For more information, see [Platform Single Sign-On to MyJFrog](https://jfrog.com/help/r/5H19DEVA7PsahAXH0xXNSg/_iPFuW3rDQk_mlAk9URBkQ).

> Note: You must be a Platform Admin to access the MyJFrog Portal via the JFrog Platform.

Log into the JFrog Platform, and in the left navigation bar of the **Application** module, click **MyJFrog Portal**.
This opens the **MyJFrog Portal** in a new tab in your browser.

4. Select **Settings** from the left navigation menu.

5. Select the **JFrog Cloud Log Streaming** tab.

6. Turn on the **Log Streaming** toggle.

7. Select **Dataset**.

![dataset.png](assets/dataset.png)

8. Enter the **Dataset API key** and the **Dataset HTTP event collection URL**. You can find the Dataset HTTP event collection URL in the official [Dataset documentation](https://app.scalyr.com/help/api?teamToken=d4woO_fl5pNaCF1rJ0xihQ--#addEvents).

9. Click **Save**.