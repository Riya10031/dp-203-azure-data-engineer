# Lab 17: Get started with Azure Stream Analytics

## Lab Scenario

In this lab, you'll provision an Azure Stream Analytics job in your Azure subscription, and use it to query and summarize a stream of real-time event data and store the results in Azure Storage.

### Objectives

After completing this lab, you will be able to:

 - View the streaming data source.
 - Create an Azure Stream Analytics job
 - Create an input for the event stream
 - Create an output for the blob store
 - Create a query
 - Run the streaming job

### Estimated timing: 30 minutes
  
### Architecture Diagram

   ![Azure portal with a cloud shell pane](./Lab-Scenario-Preview/media/lab17.png)

## Task 1: Provision Azure resources

In this task, you'll capture a stream of simulated sales transaction data, process it, and store the results in a blob container in Azure Storage. You'll need an Azure Event Hubs namespace to which streaming data can be sent, and an Azure Storage account in which the results of stream processing will be stored.

You'll use a combination of a PowerShell script and an ARM template to provision these resources.

1. In a web browser, sign into the [Azure portal](https://portal.azure.com) at `https://portal.azure.com`.

1. Use the **[\>_]** button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal.

    ![Azure portal with a cloud shell pane](./images/cloudshell(1).png)

    >**Note:** If you are not able to see the **[\>_]** button, click on the **ellipses (1)** to the right of the search bar at the top of the page and then select **Cloud Shell (2)** from the drop down options.

    ![Azure portal with a cloud shell pane-ellipses](./images/cloudshell-ellipses.png)

1. Selecting a ***PowerShell*** environment and creating storage if prompted. The cloud shell provides a command line interface in a pane at the bottom of the Azure portal, as shown here:

    ![Azure portal with a cloud shell pane](./images/powershell.png)


1. On the **Getting Started** pop-up, select the following information:-

    - Select **Mount storage account (1)**
    - Storage account subscription: **Select the existing subscription (2)**
    - Select **Apply (3)**

        ![Azure portal with a cloud shell pane](./images/gettingstarted.png)

1. On the **Mount storage account** pop-up, select the following:

    - **We will create a storage account for you (1)**
    - Select **Next (2)**

        ![Azure portal with a cloud shell pane](./images/mount-storageaccount.png)

1. Note that you can resize the cloud shell by dragging the separator bar at the top of the pane, or by using the **&#8212;**, **&#9723;**, and **X** icons at the top right of the pane to minimize, maximize, and close the pane. For more information about using the Azure Cloud Shell, see the [Azure Cloud Shell documentation](https://docs.microsoft.com/azure/cloud-shell/overview)

    ![Azure portal with a cloud shell pane](./images/close(1).png)
    
1. In the PowerShell pane, manually enter the following commands to clone this repo:

    ```
    rm -r dp-203 -f
    git clone https://github.com/MicrosoftLearning/dp-203-azure-data-engineer dp-203
    ```
1. After the repo has been cloned, enter the following commands to change to the folder for this exercise and run the **setup.ps1** script it contains:


    ```
    cd dp-203/Allfiles/labs/17
    ./setup.ps1
    ```
1. If prompted, choose which subscription you want to use (this will only happen if you have access to multiple Azure subscriptions).

1. Wait for the script to complete - this typically takes around 5 minutes, but in some cases may take longer. While you are waiting, review the [Welcome to Azure Stream Analytics](https://learn.microsoft.com/azure/stream-analytics/stream-analytics-introduction) article in the Azure Stream Analytics documentation.

## Task 2: View the streaming data source

Before creating an Azure Stream Analytics job to process real-time data, let's take a look at the data stream it will need to query.

1. When the setup script has finished running, resize or minimize the cloud shell pane so you can see the Azure portal (you'll return to the cloud shell later). Then in the Azure portal, go to the **dp203-*xxxxxxx*** resource group that it created, and notice that this resource group contains an Azure Storage account and an Event Hubs namespace.

    >**Note**: The **Location** where the resources have been provisioned - later, you'll create an Azure Stream Analytics job in the same location.

2. Re-open the cloud shell pane, and enter the following command to run a client app that sends 100 simulated orders to Azure Event Hubs:

    ```
    node ~/dp-203/Allfiles/labs/17/orderclient
    ```

3. Observe the sales order data as it is sent - each order consists of a product ID and a quantity. The app will end after sending 1000 orders, which takes a minute or so.

## Task 3: Create an Azure Stream Analytics job

Now you're ready to create an Azure Stream Analytics job to process the sales transaction data as it arrives in the event hub.

1. In the Azure portal, on the **dp203-*xxxxxxx*** page, select **+ Create** and search for `Stream Analytics job`. Then create a **Stream Analytics job** with the following properties and click on **Review + Create** and then click on **Create**.
    
    - **Basics**:
        
        - **Subscription**: Your Azure subscription
        
        - **Resource group**: Select the existing **dp203-*xxxxxxx*** resource group.
        
        - **Name**: **process-orders**
        
        - **Region**: Select the region where your other Azure resources are provisioned.
        
        - **Hosting environment**: Cloud
        
        - **Streaming units**: 1
    
    - **Storage**:
        
        - **Add storage account**: Unselected
    
    - **Tags**:
        
        - *None*
        
2. Wait for deployment to complete and then go to the deployed Stream Analytics job resource.

    <validation step="cfbef9ad-000c-4cdc-b432-dfb2a75a3f77" />

    > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
    > - If you receive a success message, you can proceed to the next task.
    > - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
    > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

    <validation step="4fef5a67-aa5f-4293-9d5e-01792f6ddf7e" />

    > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
    > - If you receive a success message, you can proceed to the next task.
    > - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
    > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

## Task 4: Create an input for the event stream

Your Azure Stream Analytics job must get input data from the event hub where the sales orders are recorded.

1. On the **process-orders** overview page. Select **Properties**, under **Inputs** select **Add input**. Then  on the **Inputs** page, click on  **+ Add input** menu to add an **Event Hub** input with the following properties:
    
    - **Input alias**: **orders**
    
    - **Select Event Hub from your subscriptions**: Selected
    
    - **Subscription**: Your Azure subscription
    
    - **Event Hub namespace**: Select the **events*xxxxxxx*** Event Hubs namespace
    
    - **Event Hub name**: Select the existing **eventhub*xxxxxxx*** event hub.
    
    - **Event Hub consumer group**: Select the existing **$Default** consumer group
    
    - **Authentication mode**: Managed Identity: System assigned
    
    - **Partition key**: *Leave blank*
    
    - **Event serialization format**: JSON
    
    - **Encoding**: UTF-8
    
2. Save the input and wait while it is created. You will see several notifications. Wait for a **Successful connection test** notification.

   <validation step="e64cb23c-ae14-4a80-b5e7-d96b34c5b5e9" />

    > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
    > - If you receive a success message, you can proceed to the next task.
    > - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
    > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

## Task 5: Create an output for the blob store

You will store the aggregated sales order data in JSON format in an Azure Storage blob container.

1. Select the **Outputs** page from the left navigation pane in the  **process-orders** Stream Analytics job page. Then use the **Add output** menu to add a **Blob storage/ADLS Gen2** output with the following properties:
    
    - **Output alias**: **blobstore**
    
    - **Select Select Blob storage/ADLS Gen2 from your subscriptions from your subscriptions**: 
    Selected
    
    - **Subscription**: Your Azure subscription
    
    - **Storage account**: Select the **store*xxxxxxx*** storage account
    
    - **Container**: Select the existing **data** container
    
    - **Authentication mode**: Managed Identity: System assigned
    
    - **Event serialization format**: JSON
    
    - **Format**: Line separated
    
    - **Encoding**: UTF-8
    
    - **Write mode**: Append as results arrive
    
    - **Path pattern**: `{date}`
    
    - **Date format**: YYYY/MM/DD
    
    - **Time format**: *Not applicable*
    
    - **Minimum rows**: 20
    
    - **Maximum time**: 0 Hours, 1 minutes, 0 seconds

2. Save the output and wait while it is created. You will see several notifications. Wait for a **Successful connection test** notification.

   <validation step="d89b45d3-05b9-4697-bc41-86bb49a59dbb" />

    > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
    > - If you receive a success message, you can proceed to the next task.
    > - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
    > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

## Task 6: Create a query

Now that you have defined an input and an output for your Azure Stream Analytics job, you can use a query to select, filter, and aggregate data from the input and send the results to the output.

1. From the left navigation pane, view the **Query** page for the **process-orders** Stream Analytics job. Then wait a few moments until the input preview is displayed (based on the sales order events previously captured in the event hub).

2. Observe that the input data includes the **ProductID** and **Quantity** fields in the messages submitted by the client app, as well as additional Event Hubs fields - including the **EventProcessedUtcTime** field that indicates when the event was added to the event hub.

3. Modify the default query as follows:

    ```
    SELECT
        DateAdd(second,-10,System.TimeStamp) AS StartTime,
        System.TimeStamp AS EndTime,
        ProductID,
        SUM(Quantity) AS Orders
    INTO
        [blobstore]
    FROM
        [orders] TIMESTAMP BY EventProcessedUtcTime
    GROUP BY ProductID, TumblingWindow(second, 10)
    HAVING COUNT(*) > 1
    ```

    Observe that this query uses the **System.Timestamp** (based on the **EventProcessedUtcTime** field) to define the start and end of each 10 second *tumbling* (non-overlapping sequential) window in which the total quantity for each product ID is calculated.

4. Use the **&#9655; Test query** button to validate the query, and ensure that the **test Results** status indicates **Success** (even though no rows are returned).

   >**Note**: After running the query if you get an output with rows, kindly click on refresh in the **Input Preview** section and click on **Test query** again.

5. Save the query.

## Task 7: Run the streaming job

OK, now you're ready to run the job and process some real-time sales order data.

1. View the **Overview** page for the **process-orders** Stream Analytics job, and on the **Properties** tab review the **Inputs**, **Query**, **Outputs**, and **Functions** for the job. If the number of **Inputs** and **Outputs** is 0, use the **&#8635; Refresh** button on the **Overview** page to display the **orders** input and **blobstore** output.

2. Select the **&#9655; Start Job** button, make sure **Now** is selected, select **Start** and start the streaming job now. Wait until you are notified that the streaming job started successfully.

   >**Note**: This might take some time. Kindly refresh the page to check the latest status.

3. Re-open the cloud shell pane, reconnecting if necessary, and then re-run the following command to submit another 1000 orders.

    ```
    node ~/dp-203/Allfiles/labs/17/orderclient
    ```

4. While the app is running, in the Azure portal, return to the page for the **dp203-*xxxxxxx*** resource group, and select the **store*xxxxxxxxxxxx*** storage account.

5. In the pane on the left of the storage account blade, select the **Containers** tab.

6. Open the **data** container, and use the **&#8635; Refresh** button to refresh the view until you see a folder with the name of the current year.

7. In the **data** container, navigate through the folder hierarchy, which includes the folder for the current year, with subfolders for the month and day.

8. In the folder for the hour, note the file that has been created, which should have a name similar to **0_xxxxxxxxxxxxxxxx.json**.

9. On the **...** menu for the file (to the right of the file details), select **View/edit**, and review the contents of the file; which should consist of a JSON record for each 10 second period, showing the number of orders processed for each product ID, like this:

    ```
    {"StartTime":"2022-11-23T18:16:25.0000000Z","EndTime":"2022-11-23T18:16:35.0000000Z","ProductID":6,"Orders":13.0}
    {"StartTime":"2022-11-23T18:16:25.0000000Z","EndTime":"2022-11-23T18:16:35.0000000Z","ProductID":8,"Orders":15.0}
    {"StartTime":"2022-11-23T18:16:25.0000000Z","EndTime":"2022-11-23T18:16:35.0000000Z","ProductID":5,"Orders":15.0}
    {"StartTime":"2022-11-23T18:16:25.0000000Z","EndTime":"2022-11-23T18:16:35.0000000Z","ProductID":1,"Orders":16.0}
    {"StartTime":"2022-11-23T18:16:25.0000000Z","EndTime":"2022-11-23T18:16:35.0000000Z","ProductID":3,"Orders":10.0}
    {"StartTime":"2022-11-23T18:16:25.0000000Z","EndTime":"2022-11-23T18:16:35.0000000Z","ProductID":2,"Orders":25.0}
    {"StartTime":"2022-11-23T18:16:25.0000000Z","EndTime":"2022-11-23T18:16:35.0000000Z","ProductID":7,"Orders":13.0}
    {"StartTime":"2022-11-23T18:16:25.0000000Z","EndTime":"2022-11-23T18:16:35.0000000Z","ProductID":4,"Orders":12.0}
    {"StartTime":"2022-11-23T18:16:25.0000000Z","EndTime":"2022-11-23T18:16:35.0000000Z","ProductID":10,"Orders":19.0}
    {"StartTime":"2022-11-23T18:16:25.0000000Z","EndTime":"2022-11-23T18:16:35.0000000Z","ProductID":9,"Orders":8.0}
    {"StartTime":"2022-11-23T18:16:35.0000000Z","EndTime":"2022-11-23T18:16:45.0000000Z","ProductID":6,"Orders":41.0}
    {"StartTime":"2022-11-23T18:16:35.0000000Z","EndTime":"2022-11-23T18:16:45.0000000Z","ProductID":8,"Orders":29.0}
    ...
    ```

10. In the Azure Cloud Shell pane, wait for the order client app to finish.

11. In the Azure portal, refresh the file to see the full set of results that were produced.

12. Return to the **dp203-*xxxxxxx*** resource group, and re-open the **process-orders** Stream Analytics job.

13. At the top of the Stream Analytics job page, use the **&#11036; Stop Job** button to stop the job, confirming as **Yes** when prompted.

  <validation step="d40d2d10-d40b-42eb-a29a-22c09818cd44" />

    > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
    > - If you receive a success message, you can proceed to the next task.
    > - If not, carefully read the error message and retry the step, following the instructions in the lab guide. 
    > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

## Review

In this lab, you have accomplished the following:
 - View the streaming data source.
 - Create an Azure Stream Analytics job
 - Create an input for the event stream
 - Create an output for the blob store
 - Create a query
 - Run the streaming job

## You have successfully completed the lab.
