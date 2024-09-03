# Lab 19: Create a realtime report with Azure Stream Analytics and Microsoft Power BI

## Lab-Scenario

Data analytics solutions often include a requirement to ingest and process *streams* of data. Stream processing differs from batch processing in that streams are generally *boundless* - in other words they are continuous sources of data that must be processed perpetually rather than at fixed intervals.

Azure Stream Analytics provides a cloud service that you can use to define a *query* that operates on a stream of data from a streaming source, such as Azure Event Hubs or an Azure IoT Hub. You can use an Azure Stream Analytics query to process a stream of data and send the results directly to Microsoft Power BI for realtime visualization.

In this exercise, you'll use Azure Stream Analytics to process a  stream of sales order data, such as might be generated from an online retail application. The order data will be sent to Azure Event Hubs, from where your Azure Stream Analytics job will read and summarize the data before sending it to Power BI, where you will visualize the data in a report.

### Objectives

After completing this lab, you will be able to:

 - Create a Power BI workspace.
 - Use Azure Stream Analytics to process streaming data.
 - Visualize the streaming data in Power BI.

### Estimated timing: 45 minutes

### Architecture Diagram

   ![Azure portal with a cloud shell pane](./Lab-Scenario-Preview/media/lab19.png)

## Task 1: Provision Azure resources

In this exercise, you'll need an Azure Synapse Analytics workspace with access to data lake storage and a dedicated SQL pool. You'll also need an Azure Event Hubs namespace to which the streaming order data can be sent.

You'll use a combination of a PowerShell script and an ARM template to provision these resources.

1. In a web browser, sign into the [Azure portal](https://portal.azure.com) at `https://portal.azure.com`.
2. Use the **[\>_]** button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal.

    ![Azure portal with a cloud shell pane](./images/25-1.png)

    >**Note:** If you are not able to see the **[\>_]** button, click on the **ellipses (1)** to the right of the search bar at the top of the page and then select **Cloud Shell (2)** from the drop down options.

    ![Azure portal with a cloud shell pane-ellipses](./images/cloudshell-ellipses.png)

3. Selecting a ***PowerShell*** environment and creating storage if prompted. The cloud shell provides a command line interface in a pane at the bottom of the Azure portal, as shown here:

    ![Azure portal with a cloud shell pane](./images/L19T1S3.png)

    > **Note**: If you have previously created a cloud shell that uses a *Bash* environment, use the the drop-down menu at the top left of the cloud shell pane to change it to ***PowerShell***.

    ![Azure portal with a cloud shell pane](./images/25-4.png)


 4. In the Getting Started menu, choose **No storage account required (1)**, select your default **Subscription (2)** from the dropdown and click on **Apply (3)**.

    ![Azure portal with a cloud shell pane](./images/L19T1S4.png)

5. Note that you can resize the cloud shell by dragging the separator bar at the top of the pane, or by using the **&#8212;**, **&#9723;**, and **X** icons at the top right of the pane to minimize, maximize, and close the pane. For more information about using the Azure Cloud Shell, see the [Azure Cloud Shell documentation](https://docs.microsoft.com/azure/cloud-shell/overview)

    ![Azure portal with a cloud shell pane](./images/25-5.png)

6. In the PowerShell pane, enter the following commands to clone the repo containing this exercise:

    ```
    rm -r dp-203 -f
    git clone https://github.com/MicrosoftLearning/dp-203-azure-data-engineer dp-203
    ```

7. After the repo has been cloned, enter the following commands to change to the folder for this exercise and run the **setup.ps1** script it contains:

    ```
    cd dp-203/Allfiles/labs/19
    ./setup.ps1
    ```

8. If prompted, choose which subscription you want to use (this will only happen if you have access to multiple Azure subscriptions).

9. While you are waiting for the script to complete, continue with the next task.

  <validation step="1a5e7a33-b485-4ed2-8a73-0baa1faffc34" />
  
  >**Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
  
  > - If you receive a success message, you can proceed to the next task.
  > - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
  > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

## Task 2: Create a Power BI workspace

In the Power BI service, you organize datasets, reports, and other resources in *workspaces*. Every Power BI user has a default workspace named **My Workspace**, which you can use in this exercise; but it's generally good practice to create a workspace for each discrete reporting solution you want to manage.

1. Sign into the Power BI service at [https://app.powerbi.com/](https://app.powerbi.com/) using your credentials.

1. Click on **Continue** to get started and provide a **10 digit phone number** and click on **Get started** and again click on **Get started** to confirm the details.

1. Now select the profile icon and select **Start trail**.

1. Click on **Start trail** and **Got it** for subsequent pop-ups respectively.

1. On the **Workspaces** page, select **+ New workspace**:

    ![A screenshot of the Create workspace tab in Power BI.](./images/workspace1.png) 

1. Create a new workspace with name **mslearn-streaming** and click on **Apply**.

    ![A screenshot of the Create workspace tab in Power BI.](./images/workspace2.png)
   
1. When viewing your workspace, note its globally unique identifier (GUID) in the page URL (which should be similar to `https://app.powerbi.com/groups/<GUID>/list`). You will need this GUID later.

  <validation step="e073b771-eca2-47f0-b2d3-274191fca853" />

  >**Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
  
  > - If you receive a success message, you can proceed to the next task.
  > - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
  > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

## Task 3: Use Azure Stream Analytics to process streaming data

An Azure Stream Analytics job defines a perpetual query that operates on streaming data from one or more inputs and sends the results to one or more outputs.

### Task 3.1: Create a Stream Analytics job

1. Switch back to the browser tab containing the Azure portal, and when the script has finished, note the region where your **dp203-*xxxxxxx*** resource group was provisioned.

2. On the **Home** page of the Azure portal, select **+ Create a resource** and search for `Stream Analytics job`. Then create a **Stream Analytics job** with the following properties and click on **Review+Create**.
    - **Subscription**: Retain the existing subscription
    - **Resource group**: Select the existing **dp203-xxxxxxx** resource group.
    - **Name**: `stream-orders`
    - **Region**: Select the region where your **dp203-xxxxxxx** resource group was provisioned.
    - **Hosting environment**: Cloud
    - **Streaming units**: 1
   
    ![A screenshot of the Create workspace tab in Power BI.](./images/stream1.png)

3. Click on **Create**

4. Wait for deployment to complete and then go to the deployed Stream Analytics job resource.

  <validation step="8665d82e-4eec-4f68-84ad-af62cd131736" />

  >**Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
  
  > - If you receive a success message, you can proceed to the next task.
  > - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
  > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

### Task 3.2: Create an input for the event data stream

1. On the **stream-orders** overview page, select **Add input**. Then  on the **Inputs** page, use the **Add stream input** menu to add an **Event Hub** input with the following properties:
    - **Input alias**: `orders`
    - **Select Event Hub from your subscriptions**: Selected
    - **Subscription**: Retain the existing subscription
    - **Event Hub namespace**: Select the **events*xxxxxxx*** Event Hubs namespace
    - **Event Hub name**: Select the existing **eventhub*xxxxxxx*** event hub.
    - **Event Hub consumer group**: Select the existing **$Default** consumer group
    - **Authentication mode**: Create system assigned managed identity
    - **Partition key**: *Leave blank*
    - **Event serialization format**: JSON
    - **Encoding**: UTF-8
2. Save the input and wait while it is created. You will see several notifications. Wait for a **Successful connection test** notification.

  <validation step="c2612022-587b-40fa-92e0-5ebd5629b6e2" />

  >**Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
  
  > - If you receive a success message, you can proceed to the next task.
  > - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
  > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

### Task 3.3: Create an output for the Power BI workspace

1. View the **Outputs** page for the **stream-orders** Stream Analytics job. Then use the **Add** menu to add an **Power BI** output with the following properties:
    - **Output alias**: `powerbi-dataset`
    - **Provide Power BI settings manually**: Selected
    - **Group workspace**: *The GUID for your workspace*
    - **Authentication mode**: *Select* **User token** *and then use the* **Authorize** *button at the bottom to sign into your Power BI account*
    - **Dataset name**: `realtime-data`
    - **Table name**: `orders`

2. Save the output and wait while it is created. You will see several notifications. Wait for a **Successful connection test** notification.

  <validation step="6c993d71-c97c-447d-bcb5-e319eef38b37" />

  >**Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
  
  > - If you receive a success message, you can proceed to the next task.
  > - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
  > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

### Task 3.4: Create a query to summarize the event stream

1. View the **Query** page for the **stream-orders** Stream Analytics job.
2. Modify the default query as follows:

    ```
    SELECT
        DateAdd(second,-5,System.TimeStamp) AS StartTime,
        System.TimeStamp AS EndTime,
        ProductID,
        SUM(Quantity) AS Orders
    INTO
        [powerbi-dataset]
    FROM
        [orders] TIMESTAMP BY EventEnqueuedUtcTime
    GROUP BY ProductID, TumblingWindow(second, 5)
    HAVING COUNT(*) > 1
    ```

    Observe that this query uses the **System.Timestamp** (based on the **EventEnqueuedUtcTime** field) to define the start and end of each 5 second *tumbling* (non-overlapping sequential) window in which the total quantity for each product ID is calculated.

3. Save the query.

### Task 3.5: Run the streaming job to process order data

1. View the **Overview** page for the **stream-orders** Stream Analytics job, and on the **Properties** tab review the **Inputs**, **Query**, **Outputs**, and **Functions** for the job. If the number of **Inputs** and **Outputs** is 0, use the **&#8635; Refresh** button on the **Overview** page to display the **orders** input and **powerbi-dataset** output.
2. Select the **&#9655; Start** button, and start the streaming job now. Wait until the status changed to **Running** in the overview page.
3. Re-open the cloud shell pane and run the following command to submit 100 orders.

    ```
    node ~/dp-203/Allfiles/labs/19/orderclient
    ```

4. While the order client app is running, switch to the Power BI app browser tab and view your workspace.
5. Refresh the Power BI app page until you see the **realtime-data** dataset in your workspace. This dataset is generated by the Azure Stream Analytics job.

  <validation step="a216e391-6b17-48d1-b37b-128a3bc9ab61" />

  >**Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
  
  > - If you receive a success message, you can proceed to the next task.
  > - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
  > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

## Task 4: Visualize the streaming data in Power BI

Now that you have a dataset for the streaming order data, you can create a Power BI dashboard that represents it visually.

1. In the **+ New** drop-down menu for your workspace, select **Dashboard**, and create a new dashboard named **Order Tracking**.
2. In the **&#9999;&#65039; Edit** menu for the **Order Tracking** dashboard, select **Add a tile**. Then in the **Add a tile** pane, select **Custom Streaming Data** and click **Next**:

    ![A screenshot of the Add a tile pane in Power BI.](./images/powerbi-stream-tile.png)

3. In the **Add a custom streaming data tile** pane, under **Your datasets**, select the **realtime-data** dataset, and click **Next**.

4. Change the default visualization type to **Line chart**. Then set the following properties and click **Next**:
    - **Axis**: EndTime
    - **Value**: Orders
    - **Time window to display**: 1 Minute

5. On the **Tile details** pane, set the **Title** to **Real-time Order Count** and click **Apply**.

6. Switch back to the browser tab containing the Azure portal, and if necessary, re-open the cloud shell pane. Then re-run the following command to submit another 100 orders.

    ```
    node ~/dp-203/Allfiles/labs/19/orderclient
    ```

7. While the order submission script is running, switch back to the browser tab containing the **Order Tracking** Power BI dashboard and observe that the visualization updates to reflect the new order data as it is processed by the Stream Analytics job (which should still be running).

   >**Note**: You can enlarge the graph for better view of visualization updates.

    ![A screenshot of a Power BI report showing a realtime stream of order data.](./images/powerbi-line-chart.png)

    You can re-run the **orderclient** script and observe the data being captured in the real-time dashboard.

## Review

In this lab, you have accomplished the following:
 - Create a Power BI workspace.
 - Use Azure Stream Analytics to process streaming data.
 - Visualize the streaming data in Power BI.

## You have successfully completed the lab.
