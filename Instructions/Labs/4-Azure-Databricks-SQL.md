# Lab 04: Use a SQL Warehouse in Azure Databricks

## Overview

SQL is an industry-standard language for querying and manipulating data. Many data analysts perform data analytics by using SQL to query tables in a relational database. Azure Databricks includes SQL functionality that builds on Spark and Delta Lake technologies to provide a relational database layer over files in a data lake.

In this lab, you'll learn about Azure Databricks, which provides SQL Warehouses that enable data analysts to work with data using familiar relational SQL queries.
 
### Lab Objectives

In this lab, you will perform:

 - Task 1: Provision an Azure Databricks workspace
 - Task 2: View and start a SQL Warehouse
 - Task 3: Create a database schema
 - Task 4: Create a table
 - Task 5: Create a query
 - Task 6: Create a dashboard

## Task 1: Provision an Azure Databricks workspace

In this task, you'll use a script to provision a new Azure Databricks workspace.

1. In a web browser, navigate to the **Azure portal**, open the following URL in the address bar, and sign in with the Lab credentials if you are not already signed in.

   ```
   https://portal.azure.com
   ```
   
1. Use the **[\>_]** **icon** to the right of the search bar to create a new **Cloud Shell** in the Azure portal.

   ![Azure portal with a cloud shell pane](./images/data-brick-gt-lab1-ex1-g1.png)

1. In the **Welcome to Azure Cloud Shell** pane, select **PowerShell**.

   ![Azure portal with a cloud shell pane](./images/data-brick-gt-lab1-ex1-g2.png)

1. Within the Getting started pane, select **Mount storage account (1)**, select your **Storage account subscription (2)** from the dropdown and click **Apply (3)**.

   ![](./images/data-brick-gt-lab1-ex1-g3.png)

1. In the **Mount storage account** pane, select **I want to create a storage account (1)** and click **Next (2)**.

   ![](./images/data-brick-gt-lab1-ex1-g4.png)

1. In the **Create storage account** pane, provide the following details:

   - **Subscription (1)**: Select the defualt **Subscription**  
   - **Resource group (2)**: Select **Azure-Databricks**  
   - **Region (3)**: Select **(US) East US**  
   - **Storage account name (4)**: Enter **storage<inject key="DeploymentID" enableCopy="false"/>**  
   - **File share (5)**: Enter **fileshare1**  
   - select **Create (6)**.

      ![](./images/data-brick-gt-lab1-ex1-g5.png)

1. In the **Deployment is in progress** notification, wait for the PowerShell terminal to start.

   ![](./images/data-brick-gt-lab1-ex1-g6.png)

1. In the PowerShell pane, paste the following **commands** and click **Enter** to clone this repo:

   ```
   rm -r dp-203-azure-data-engineer -f
   git clone -b guidedlabs --single-branch https://github.com/CloudLabs-MOC/dp-203-azure-data-engineer.git
   ```

   ![](./images/data-brick-gt-lab1-ex1-g7.png)

1. After the repo has been cloned, enter the following commands to change to the folder for this lab and run the **setup.ps1** script it contains:

   ```
   cd dp-203-azure-data-engineer/Allfiles/labs/26
   ./setup.ps1
   ```

1. Wait for the script to complete (this may take a few minutes), and while waiting, review the following documentation.

   ```
   https://learn.microsoft.com/azure/databricks/sql/
   ```

1. In the **Cloud Shell** window, select the **X** at the top right corner to close it after the script execution completes.

   ![Azure portal with a cloud shell pane](./images/data-brick-gt-lab1-ex1-g10.png)

## Task 2: View and start a SQL Warehouse

In this task, you will launch the workspace, and you'll view and start SQL Warehouse.

1. In the **Search resources, services, and docs (G+/) (1)** box, enter **dp203-<inject key="DeploymentID" enableCopy="false"/>**, and then select the **Resource group (2)**.

   ![](./images/data-brick-gt-lab1-ex1-g11.png)
 
1. In the **Overview (1)** page, select the **databricks<inject key="DeploymentID" enableCopy="false"/> (2)** resource.

   ![Create storage by clicking confirm.](./images/data-brick-gt-lab1-ex1-g12.png)

1. In the **Overview** page, select **Launch Workspace**.

   ![Create storage by clicking confirm.](./images/data-brick-gt-lab1-ex1-g13.png)

   > **Note**: In the Databricks Workspace portal, dismiss any tips or notifications that appear, and continue with the lab instructions.

1. View the Azure Databricks workspace portal and note that the sidebar on the left side contains links for the various types of tasks you can perform.
  
1. Select **SQL Warehouses (1)** from the left navigation pane, and then choose the **SQL Warehouses (2)** tab under Compute.

   ![](./images/data-brick-gt4-lab4-ex1-g1.png)

1. Observe that the workspace already includes a SQL Warehouse named **Serverless Starter Warehouse**.

1. Select the **More options (1)** menu for the SQL Warehouse, and then choose **Edit (2)**.

   ![](./images/data-brick-gt4-lab4-ex1-g2.png)

1. A Page appears where you can configure the Cluster size, set the **Cluster size** property to **2X-Small (1)**, and **Save (2)** your changes.

   ![](./images/data-brick-gt4-lab4-ex1-g3.png)
    
1. Use the **Start** button to start the SQL Warehouse (which may take a minute or two).

   ![](./images/data-brick-gt4-lab4-ex1-g4.png)

1. Now your SQL Warehouse will be up and running.

   ![](./images/ad-lab4-6.png)

   > **Note**: If your SQL Warehouse fails to start, your subscription may have insufficient quota in the region where your Azure Databricks workspace is provisioned. See [Required Azure vCPU quota](https://docs.microsoft.com/azure/databricks/sql/admin/sql-endpoints#required-azure-vcpu-quota) for details. If this happens, you can try requesting a quota increase as detailed in the error message when the warehouse fails to start. Alternatively, you can try deleting your workspace and creating a new one in a different region. You can specify a region as a parameter for the setup script like this: `./setup.ps1 eastus`

## Task 3: Create a database schema

In this task, you will be creating a database schema in your Azure Databricks Portal.

1. Select **SQL Editor** from the left navigation pane.

   ![](./images/data-brick-gt4-lab4-ex1-g5.png)

1. Select the **Schema browser (1)** icon, and then expand **hive_metastore (2)**.

   ![](./images/data-brick-gt4-lab4-ex1-g6.png)

1. In the **Create new** section, select **SQL Query**.

   ![](./images/data-brick-gt4-lab4-ex1-g7.png)

1. Select **Got it** to continue.

   ![](./images/data-brick-gt4-lab4-ex1-g8.png)
   
1. In the **New query** pane, enter the following SQL code:

    ```sql
    CREATE SCHEMA hive_metastore.adventureworks;
    ```    
1. Use the **&#9658; Run (1000)** button to run the SQL code.

   ![](./images/data-brick-gt4-lab4-ex1-g9.png)

1. Verify the schema creation by confirming the **Table (1)** section shows *OK*, and then expand **adventureworks (2)** under **hive_metastore**.

   ![](./images/data-brick-gt4-lab4-ex1-g10.png)

You can use the **default** database for your tables, but when building an analytical data store, it's best to create custom databases for specific data.

## Task 4: Create a table

In this task, you will create a table schema using an external file.

1. In the LabVM, open the following URL in a new browser tab to download the **products.csv** file:

   ```
   https://github.com/CloudLabs-MOC/dp-203-azure-data-engineer/blob/guidedlabs-Azure-Databricks/Allfiles/labs/26/data/products.csv
   ```

1. Click on the **download** icon to download the file to your Lab VM.

   ![](./images/data-brick-gt4-lab4-ex1-g11.png)

1. In the Azure Databricks workspace portal, in the sidebar, select **+ New (1)** and then select **Add or upload data (2)**

    ![](./images/data-brick-gt4-lab4-ex1-g12.png)

1. In order to upload the **products.csv** file, click on **Create or modify table**.

   ![](./images/data-brick-gt4-lab4-ex1-g13.png)

1. Then click on **browse** to upload the file that you have downloaded to your computer.

   ![](./images/data-brick-gt4-lab4-ex1-g14.png)

1. Once **products.csv** file has been uploaded **(1)**. In the **Add data** page, select the **hive_metastore (2)** from the Catalog dropdown, select the **adventureworks (3)** database from the schema dropdown, and set the table name to **products (4)**. Then select **Create table (5)** on the bottom right corner of the page.

   ![](./images/data-brick-gt4-lab4-ex1-g15.png)

1. Now the table named **products** has been created under **adventureworks** database, review the table details.

   ![](./images/data-brick-gt4-lab4-ex1-g16.png)

The ability to create a table by importing data from a file makes it easy to populate a database. You can also use Spark SQL to create tables using code. The tables themselves are metadata definitions in the hive metastore, and the data they contain is stored in Delta format in Databricks File System (DBFS) storage.

## Task 5: Create a query

In this task, you will create a query in the AdventureWorks database, which was created earlier.

1. In the sidebar, select **+ New (1)** and then select **Query (2)**.

   ![](./images/data-brick-gt4-lab4-ex1-g17.png)

1. In the **New query** pane, enter the following SQL code:

   ```sql
   SELECT ProductID, ProductName, Category
   FROM hive_metastore.adventureworks.products; 
   ```

1. Use the **&#9658;Run (1000)** button to run the SQL code.

   ![](./images/data-brick-gt4-lab4-ex1-g18.png)

1. When the query has completed, review the table of results.

   ![](./images/data-brick-gt4-lab4-ex1-g19.png)

## Task 6: Create a dashboard

In this task, you will be creating a dashboard to visualize the data of the Products dataset.

1. In the sidebar, select **Catalog (1)** ,select **hive_metastore > adventureworks > products (2)**.

1. In the products, select dropdown **Create (3) > Dashboard (4)**

   ![](./images/24-06-25-l4-13.png)

1. Verify the dashboard name is updated to **Adventure Works Products**.

   ![](./images/data-brick-gt4-lab4-ex1-g21.png)

1. In the visualization editor, configure the chart as follows:
   - Select the chart icon (1).
   - Set **Dataset (2)** to *products*.
   - Set **Visualization (3)** to *Bar*.
   - Set **X axis (4)** to *COUNT(*)*.
   - Set **Y axis (5)** to *Category*.

      ![](./images/data-brick-gt4-lab4-ex1-g22.png)

1. Select **Publish** to publish the dashboard.

   ![](./images/data-brick-gt4-lab4-ex1-g23.png)

1. In the **Publish Adventure Works Products** dialog box, select **Share data permission (default) (1)**, and then choose **Publish (2)**.

   ![](./images/data-brick-gt4-lab4-ex1-g24.png)

1. Select **Close** to exit the sharing dialog box.

   ![](./images/data-brick-gt4-lab4-ex1-g25.png)

1. Select **Dashboards (1)** from the left navigation pane, and then choose **Adventure Works Products (2)**.

   ![](./images/data-brick-gt4-lab4-ex1-g26.png)
   
Dashboards are a great way to share data tables and visualizations with business users. You can schedule the dashboards to be refreshed periodically and emailed to subscribers.

> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
> - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
> - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
> - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help

<validation step="0e124d80-b14d-4de2-b75e-47fb394bba8b" />

 ## Summary

In this lab, you have performed a series of tasks to work with Azure Databricks. You provisioned an Azure Databricks workspace, viewed and started a SQL Warehouse, created a database and a table, wrote a query, and created a dashboard to visualize your data.
 
 ## You have successfully completed the lab.
