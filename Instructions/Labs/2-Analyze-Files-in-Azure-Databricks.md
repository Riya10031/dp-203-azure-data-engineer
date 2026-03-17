# Lab 02: Use Spark in Azure Databricks

## Overview

Azure Databricks is a Microsoft Azure-based version of the popular open-source Databricks platform. Azure Databricks is built on Apache Spark and offers a highly scalable solution for data engineering and analysis tasks that involve working with data in files. One of the benefits of Spark is support for a wide range of programming languages, including Java, Scala, Python, and SQL, making Spark a very flexible solution for data processing workloads including data cleansing and manipulation, statistical analysis and machine learning, and data analytics and visualization.

In this lab, you'll learn about Apache Spark clusters to process data in parallel on multiple nodes. As in many Spark environments, Databricks supports the use of notebooks to combine notes and interactive code cells that you can use to explore data.

### Lab Objectives

In this lab, you will perform:

 - Task 1: Provision an Azure Databricks workspace
 - Task 2: Create a cluster
 - Task 3: Explore data using a notebook

## Task 1: Provision an Azure Databricks workspace

In this task, you'll use a script to provision a new Azure Databricks workspace.

> **Tip**: If you already have a *Standard* or *Trial* Azure Databricks workspace, you can skip this procedure and use your existing workspace.

1. Use the **[\>_]** button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal.

    ![Azure portal with a cloud shell pane](./images/24-06-25-l1-cl.png)

    >**Note:** If you are not able to see the **[\>_]** button, click on the **ellipses (1)** to the right of the search bar at the top of the page and then select **Cloud Shell (2)** from the drop down options.

    ![Azure portal with a cloud shell pane-ellipses](./images/cloudshell-ellipses.png)

1. Selecting a ***PowerShell*** environment and creating storage if prompted. The cloud shell provides a command line interface in a pane at the bottom of the Azure portal, as shown here:

    ![Azure portal with a cloud shell pane](./images/24-06-25-l1-1.png)

1. Within the Getting Started pane, select **Mount storage account (1)**, select your **Storage account subscription (2)** from the dropdown and click **Apply (3)**.

   ![](./images/24-06-25-l1-3.png)

1. Within the **Mount storage account** pane, select **I want to create a storage account (1)** and click **Next (2)**.

   ![](./images/24-06-25-l1-2.png)

1. If you are prompted to create storage for your Cloud Shell, ensure your **Subscription** is selected, Please make sure you have selected your **Resource Group** which is **Azure-Databricks (1)**, select **Region** from the drop-down **(US) East US (2)** and enter **storage<inject key="DeploymentID" enableCopy="false"/> (3)** for the **Storage account name** and enter **fileshare1 (4)** for the **File share name**, then click on **Create (5)**.

    ![Create storage by clicking confirm.](./images/24-06-25-l1-4.png "Create storage advanced settings")

1. You can see a pop-up appearing **Deployment is in Progress**, wait for the PowerShell terminal to start.

   ![](./images/24-06-25-l1-5.png)

1. In the PowerShell pane, enter the following commands to clone this repo:

    ```
    rm -r dp-203-azure-data-engineer -f
    git clone -b guidedlabs --single-branch https://github.com/CloudLabs-MOC/dp-203-azure-data-engineer.git
    ```

1. After the repo has been cloned, enter the following commands to change to the folder for this lab and run the **setup.ps1** script it contains:

    ```
    cd dp-203-azure-data-engineer/Allfiles/labs/24
    ./setup.ps1
    ```

1. If prompted, choose which subscription you want to use (this will only happen if you have access to multiple Azure subscriptions).

1. Wait for the script to complete - this typically takes around **5 minutes**, but **in some cases may take longer**. While you are waiting, review the [Exploratory data analysis on Azure Databricks](https://learn.microsoft.com/azure/databricks/exploratory-data-analysis/) article in the Azure Databricks documentation.

    ![Azure portal with a cloud shell pane](./images/24-06-25-l1-6.png)

1. Once the Script has completed its execution, close the **cloud shell** window by clicking on the **X**, which is located at the top right corner of the **cloud shell**

   ![Azure portal with a cloud shell pane](./images/24-06-25-l1-7.png)

## Task 2: Create a cluster

Azure Databricks is a distributed processing platform that uses Apache Spark *clusters* to process data in parallel on multiple nodes. Each cluster consists of a driver node to coordinate the work and worker nodes to perform processing tasks.

In this task, you will be using the Azure Databricks Portal to create a cluster.

> **Tip**: If you already have a cluster with a 13.3 LTS runtime version in your Azure Databricks workspace, you can use it to complete this exercise and skip this procedure.

1. In the Azure portal, in the **Search resources, services, and docs (G+/)** text box at the top of the Azure portal page, type **dp203-*xxxxxxx* (1)** resource group that was created by the script (or the resource group containing your existing Azure Databricks workspace) and select the **Resource group (2).**

   ![](./images/24-06-25-l1-8.png)
 
1. Select your Azure Databricks Service resource (named **databricks*xxxxxxx*** if you used the setup script to create it).

    ![Create storage by clicking confirm.](./images/24-06-25-l1-9.png)

1. In the **Overview** page for your workspace, use the **Launch Workspace** button to open your Azure Databricks workspace in a new browser tab, signing in if prompted.

    ![Create storage by clicking confirm.](./images/21056.png)

    > **Tip**: As you use the Databricks Workspace portal, various tips and notifications may be displayed. Dismiss these and follow the instructions provided to complete the tasks in this exercise.

1. View the **Azure Databricks workspace portal** and note that the sidebar on the left side contains icons for the various tasks you can perform.

1. Select the **+ New (1)** link in the sidebar, and then select **More (2)** ,then click on **Cluster (3)**.

    ![Create storage by clicking confirm.](./images/24-06-25-l1-10.png)
 
1. In the **New Cluster** page, create a new cluster with the following settings
    - **Cluster name**: *User Name's* cluster (the default cluster name)
    - **Cluster mode (1)**: Single Node
    - **Access mode (2)**: Single user 
    - **Single user access (3)**: with your user account selected
    - **Databricks runtime version (4)**: 13.3 LTS (Spark 3.4.1, Scala 2.12)
    - **Use Photon Acceleration (5)**: Selected
    - **Node type (6)**: Standard_DS3_v2
    - **Terminate after (7)** *30* **minutes of inactivity**

   Once all the required settings have been provided, click on **Create compute (8)**

    ![Create storage by clicking confirm.](./images/24-06-25-l1-11.png)

1. Wait for the cluster to be created. It may take a minute or two.

    ![Create storage by clicking confirm.](./images/cluster-name.png)

   >**Note**: If your cluster fails to start, your subscription may have an insufficient quota in the region where your Azure Databricks workspace is provisioned. See [CPU core limit prevents cluster creation](https://docs.microsoft.com/azure/databricks/kb/clusters/azure-core-limit) for details. If this happens, you can try deleting your workspace and creating a new one in a different region. You can specify a region as a parameter for the setup script like this: `./setup.ps1 eastus`

## Task 3: Explore data using a notebook

As in many Spark environments, Databricks supports the use of notebooks to combine notes and interactive code cells that you can use to explore data.

In this task, you will be importing a notebook to the Azure Databricks Portal.

1. In the **Azure Databricks workspace portal** for your workspace, in the sidebar on the left, select **Workspace (1)**. Then select the **&#8962; Home (2)** folder.

1. At the top of the page, in the **&#8942; (3)** menu next to your user name, select **Import (4)**.

   ![Create storage by clicking confirm.](./images/24-06-25-12-1.png)

1. Then in the **Import** dialog box, select **URL (1)** , in the **URL** import the notebook from `https://github.com/CloudLabs-MOC/dp-203-azure-data-engineer/raw/guidedlabs/Allfiles/labs/24/Databricks-Spark.ipynb` **(2)** and then click on **Import (3)**.

   ![Create storage by clicking confirm.](./images/24-06-25-12-2.png)

1. Connect the notebook to your cluster, and follow the instructions it contains; run the cells it contains to explore data in files.

   ![Create storage by clicking confirm.](./images/24-06-25-12-3.png)
   
> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
> - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
> - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
> - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help

<validation step="0fa9a722-c79c-4bf4-a1f5-726d5e03453f" />

## Summary

In this lab, you have performed essential tasks to get started with Azure Databricks. You provisioned an Azure Databricks workspace, created a cluster, and explored data using a notebook to gain insights and perform analyses.

## You have successfully completed the lab.
