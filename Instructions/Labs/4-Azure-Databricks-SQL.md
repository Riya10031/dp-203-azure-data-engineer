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
  
1. In the sidebar, under **SQL (1)**, select **SQL Warehouses (2)**.

   ![](./images/24-06-25-l4-1.png)

1. Observe that the workspace already includes a SQL Warehouse named **Serverless Starter Warehouse**.

   ![](./images/24-06-25-l4-2.png)

1. In the **Actions** (**&#8285;**) **(1)** menu for the SQL Warehouse, select **Edit (2)**.

   ![](./images/24-06-25-l4-3.png)

1. A Page appears where you can configure the Cluster size, set the **Cluster size** property to **2X-Small (1)**, and **Save (2)** your changes.

   ![](./images/24-06-25-l4-4.png)
    
1. Use the **Start** button to start the SQL Warehouse (which may take a minute or two).

   ![](./images/24-06-25-l4-5.png)

1. Now your SQL Warehouse will be up and running.

   ![](./images/ad-lab4-6.png)

> **Note**: If your SQL Warehouse fails to start, your subscription may have insufficient quota in the region where your Azure Databricks workspace is provisioned. See [Required Azure vCPU quota](https://docs.microsoft.com/azure/databricks/sql/admin/sql-endpoints#required-azure-vcpu-quota) for details. If this happens, you can try requesting a quota increase as detailed in the error message when the warehouse fails to start. Alternatively, you can try deleting your workspace and creating a new one in a different region. You can specify a region as a parameter for the setup script like this: `./setup.ps1 eastus`

## Task 3: Create a database schema

In this task, you will be creating a database schema in your Azure Databricks Portal.

1. When your SQL Warehouse is *running*, select **SQL Editor** in the sidebar.

   ![](./images/ad-lab4-7.png)

1. In the **Schema browser** pane, observe that the **hive_metastore** catalog contains a database named **default**.

   ![](./images/24-06-25-l4-6.png)
   
1. In the **New query** pane, enter the following SQL code:

    ```sql
    CREATE SCHEMA hive_metastore.adventureworks;
    ```    
1. Use the **&#9658; Run (1000)** button to run the SQL code.

   ![](./images/ad-lab4-9.png)

1. When the code has been successfully executed, in the **Schema browser** pane, use the refresh button at the bottom of the pane to refresh the list. Then expand **hive_metastore (1)** and **adventureworks (2)**, and observe that the database has been created, but contains no tables.

   ![](./images/24-06-25-l4-7.png)

You can use the **default** database for your tables, but when building an analytical data store, it's best to create custom databases for specific data.

## Task 4: Create a table

In this task, you will create a table schema using an external file.

1. Download the [**products.csv**](https://github.com/CloudLabs-MOC/dp-203-azure-data-engineer/blob/guidedlabs/Allfiles/labs/26/data/products.csv) file to your local computer to download the file press **Ctrl+S**, saving it as **products.csv**, .

1. In the Azure Databricks workspace portal, in the sidebar, select **+ New (1)** and then select **Add or upload data (2)**

    ![](./images/ad-lab4-11.png)

1. In order to upload the **products.csv** file, click on **Create or modify table**.

   ![](./images/24-06-25-l4-8.png)

1. Then click on **browse** to upload the file that you have downloaded to your computer.

   ![](./images/24-06-25-l4-9.png)

1. Once **products.csv** file has been uploaded **(1)**. In the **Add data** page, select the **hive_metastore (2)** from the Catalog dropdown, select the **adventureworks (3)** database from the schema dropdown, and set the table name to **products (4)**. Then select **Create table (5)** on the bottom right corner of the page.

   ![](./images/24-06-25-l4-10.png)

1. Now the table named **products** has been created under **adventureworks** database, review the table details.

   ![](./images/24-06-25-l4-11.png)

The ability to create a table by importing data from a file makes it easy to populate a database. You can also use Spark SQL to create tables using code. The tables themselves are metadata definitions in the hive metastore, and the data they contain is stored in Delta format in Databricks File System (DBFS) storage.

## Task 5: Create a query

In this task, you will create a query in the adventureworks database, which was created earlier.

1. In the sidebar, select **+ New (1)** and then select **Query (2)**.

   ![](./images/ad-lab4-16.png)

1. In the **Schema browser** pane, expand **hive_metastore** and **adventureworks**, and verify that the **products** table is listed.

   ![](./images/24-06-25-l4-11.png)

1. In the **New query** pane, enter the following SQL code:

   ```sql
   SELECT ProductID, ProductName, Category
   FROM hive_metastore.adventureworks.products; 
   ```

1. Use the **&#9658;Run (1000)** button to run the SQL code.

   ![](./images/24-06-25-l4-12.png)

1. When the query has completed, review the table of results.

   ![](./images/ad-lab4-18.png)

1. Use the **Save** button at the top right of the query editor to save the query.

   ![](./images/ad-lab4-19.png)
  
1. Save the query by the name **Products and Categories (1)** and then click on **Save (2)**.

   ![](./images/ad-lab4-20.png)

Saving a query makes it easy to retrieve the same data again at a later time.

## Task 6: Create a dashboard

In this task, you will be creating a dashboard to visualize the data of the Products dataset.

1. In the sidebar, select **Catalog (1)** ,select **hive_metastore > adventureworks > products (2)**.

1. In the products, select dropdown **Create (3) > Dashboard (4)**

   ![](./images/24-06-25-l4-13.png)

1. In the dialog box, change the name to **Adventure Works Products (1)**.

1. Click on the **Add a visualization** **(2)** dialog box, select the **Products (3)** dataset.

1. In the visualization editor, set the following properties:
    - **Visualization type (4)**: bar
    - **X columns (5)**: Product ID : Count

      >**Note:** Click on **+** icon beside X column and then select **Product ID** from the drop down , initially it appears as **SUM(ProductID)**, Hence click on **SUM(ProductID)** and change **SUM** to **Count**.
       
    - **Y column (6)**: Category
    - **color/Group by**: *Leave blank*

    Then **Publish (7)** the visualization.
   
    ![](./images/Dashboard4.png)
   
1. A pop-up appears, click on **Publish**.

    ![](./images/24-06-25-l4-14.png)

1. Now the Dashboard has been published successfully. Click on **here** to view the Dashboard. Alternatively, you can also view the dashboard by navigating to the **Dashboard** in the left pane.

   ![](./images/ad-lab4-23.png)

   ![](./images/ad-lab4-24.png)
   
Dashboards are a great way to share data tables and visualizations with business users. You can schedule the dashboards to be refreshed periodically and emailed to subscribers.

> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
> - Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
> - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
> - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help

<validation step="0e124d80-b14d-4de2-b75e-47fb394bba8b" />

 ## Summary

In this lab, you have performed a series of tasks to work with Azure Databricks. You provisioned an Azure Databricks workspace, viewed and started a SQL Warehouse, created a database and a table, wrote a query, and created a dashboard to visualize your data.
 
 ## You have successfully completed the lab.
