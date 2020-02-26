# Database Migration Assistant (DMA)


## Before you Begin

Your Azure subscription is limited in the amount of cores that you can provision.  Ensure that you have deleted the VMs from the previous labs before completing this lab. 


## Lab Concepts

In this lab you are going to use DMA (Data Migration Assistant) to migrate a SQL server database over to Azure SQL PaaS.  You will do this by first assessing the SQL database to ensure it can safely migrate all data, scripts and schema to Azure SQL.  Once the assessment is comlpete and any issues are resolved, you will migrate the database to Azure SQL and verify the migration.
 

After completing this lab, you should understand how to:
1. Assess an on-premises SQL Database for migration to Azure SQL Database
2. Migrate an on-premises SQL Database to Azure SQL Database

## Exercise 1 - Create a SQL Server VM image

1. Log in to the Azure portal using your account.
2. On the Azure portal, click **+Create a resource**.
3. In the search field, type **SQL Server 2017  on Windows Server 2016** and press **ENTER**.
4. Under Select a software plan choose Select the **SQL Server 2017 Standard on Windows Server 2016 Database Engine Only** and click **Create**.
5. Enter the following and then click **OK**.
    * Resource Group: *create new* **SQLMIG**
    * Virtual Machine Name: **SQLVM**
    * Region: Choose a supported region
    * Availability Options: No infrastructure redundancy required
    * Size: Select **D2s_v3**
    * Username: `Goose` 
    * Password: `Complex.Password`
    * Confirm Password: `Complex.Password`
    * Public inbound ports: **Allow selected ports**
    * Select inbound ports: **RDP (3389)**
    * Click Next
6. Click on the **SQL Server settings** blade and complete the following:
    * In the SQL connectivity drop-down, select **Public (Internet)**. This allows SQL Server connections over the internet.
    * Change the Port to **1401** to avoid using a well-known port name in the public scenario.
    * Under **SQL Authentication**, click **Enable**. The SQL Login is set to the same user name and password that you configured for the VM.
7. Click **Review + create** to complete the configuration of the SQL Server VM. Once validation passes, click **Create** to build your VM.  Note that it may take 10-15 minutes to build out your virtual machine with SQL installed.
8. While your VM is building, this is a great opportunity to take a break before you begin your SQL DB migration.

## Exercise 2 - Connect to the SQL VM

1. At the top of the Azure portal, enter **SQLVM**. When **SQLVM** appears in the search results, select it. Select the **Connect** button.
2. After selecting the Connect button, click on **Download RDP** file.
3. Enter the user name and password you specified when creating the VM. You may need to select **More choices** then **Use a different account** to specify the credentials you entered when you created the VM.
4. Select **OK**.
5. Once the desktop renders, click **No** on the Networks blade.

## Exercise 3 - Map a drive to an Azure Files Share

Before you can complete this section, you will need to map a drive to an Azure File Share to obtain sample data.  

1. On SQLVM, open a command prompt.
2. Key in (cut and paste) the following and hit **enter**:

    `net use Z: \\wagsazurefiles.file.core.windows.net\sampledata /u:AZURE\wagsazurefiles tCfYh37xGNjIc0czqfTW9+kUHIIhlxRUPh9h4YtD/hh7FiFPn1v32RH7uV0a83E6nAa6kkVU6d+nAAeoBItpJg==`

3. Once Z: is mapped, change to the Z: drive.
4. Confirm that you can see a file named sampledata.txt.

## Exercise 4 - Connect to SQL

1. Back on the desktop click the **Start Button**,  under the letter "M" choose **Microsoft SQL Server Tools 17** and then **Microsoft SQL Server Management Studio**.  It might take a few seconds for Management Studio to fire up.
2. In the **Connect to Server** or **Connect to Database Engine** dialog box, ensure the Server name value is  **SQLVM**.
3. In the Authentication box, select **SQL Server Authentication**.
4. In the Login box, type the name of the SQL login you created during  the previous exercise.
5. In the Password box, type the password of the login from the previous exercise.
6. Click **Connect**.

## Exercise 5 - Create a new Database and Import Data

1. Under **SQLVM**, right-click **Databases** then **New Database ...**
2. Enter **SampleData** as the Database name and click **OK**.
3. Once the database is created, right-click **SampleData**, then **Tasks**, then **Import Flat File**.
4. On the Introduction screen click **Next**.
5. On the **Specify Input File** screen select click **Browse**, then select  the Z: Drive, then **sampledata.txt**, then click **Open**, then click **Next**.
6. Review the information on the **Preview Data screen** and click **Next**.
7. On the **Modify Columns screen** set *zip* as the Primary Key, enable **Allow Nulls** on all columns, and then click **Next**.
8. Click **Finish** on the Summary screen.
9. Click **Close** on the Results screen.

## Exercise 6 - Install the Data Migration Assistant

1. From  **SQLVM**, copy the Data Migration Assitant MSI from the Z: to SQLVM's Downloads folder on the local C: drive.  
2. Install the Data Migration Assistant by running the MSI.  Accept all default options.
3. On the Completed screen, check the box for **Launch Microsoft Data Migration Assistant** and click **Finish**.

## Exercise 7 - Assess the simulated on-premises database

Before you can migrate data from an on-premises SQL Server  to a single database or pooled database in Azure SQL Database, you need to assess the SQL Server database for any blocking issues that might prevent migration.

1. In DMA, select the  (+) icon, and then select  **Assessment** as the project type.
2. Specify **SampleAssessment** as the project name.
3. In the Source server type text box ensure that  **SQL Server** is selected, and in the Target server type text box, ensure **Azure SQL Database** is selected. Click  **Create** to create the project.
4. Select **Next** on the Options screen.
5. On the Select sources screen, enter the following and then select **Connect**:
    * Server name: Enter **SQLVM** 
    * Authentication type: **SQL Server Authentication**
    * In the **Username** box, type `Goose` 
    * In the **Password** box, type `Complex.Password` 
    * Uncheck **Encrypt connection** under **Connection properties**.  Under normal circumstances we would use this option but we did not configure certificates on the source SQL server.
    * Click **Connect**.
6. Under *Add sources* select **SampleData** and the click **Add**.
7. Select **Start Assessment**.
8. Review the results and then select **Compatibility issues** radio button in the upper left-hand corner to see if there are any compatibility issues with your database.

## Exercise 8 -Provision an Azure SQL database

Before you migrate, a target Azure SQL database needs to be provisioned..

1. From the Azure Portal, Select **+Create a resource** in the upper left-hand corner of the Azure portal.
2. Select **Databases** and then select **SQL Database**.
3. In the **Create SQL Database** form, type or select the following values:
    * Resource group: *Create new*, type **AzureSQL**.
    * Database name: Enter **Cloud**.
    * Server:  
        * Select *Create new*, type *yourinitials*+*todayshortdate*. Example: `abc032019`
        * Server admin login: `Goose`
        * Password: `Complex.Password`
        * Confirm Password: `Complex.Password`
        * Location: Select the same region as your other resources
        * Click **Ok**
    * Under **Compute + storage** click *Configure database*, choose **Basic**, and then click **Apply**.
4. Select **Review + Create** and then **Create**.
5. Once your database server is created, click **Go to resource**.
6. In the Azure Portal, copy to clipboard the FQDN of the database server, listed as 'Server Name' on the overview section  (i.e. `abc032019.database.windows.net`).
7. Next you need to allow connections to your new Azure SQLDB by opening the firewall:
    * Click on **Set Server firewall**
    * Click **ON** under **Allow Azure Services and resources to access this server**.
    * Create the following Rule:
        * Rule Name: **Everyone**
        * Start IP: **0.0.0.0**
        * End IP: **255.255.255.255**
    * Click **Save**

In production, you would limit opening access to only the specific IPs or subnets that require access to the database.  You should never open the database to all subnets as you just did in this lab.  This was only done in the lab to allow quick access to the database.

## Exercise 9 - Migrate the sample schema

After you're comfortable with the assessment and satisfied that the selected database is a viable candidate for migration to a single database or pooled database in Azure SQL Database, use DMA to migrate the schema to Azure SQL Database.

1. Go back to the SQLVM desktop
1. In the Data Migration Assistant, select the **New (+)** icon, and then under Project type, select **Migration**.
2. Specify the following and Click **Create**.
    * **SQLMIG** as the project name
    * **SQL Server** in the Source server type text box
    * **Azure SQL Database** in the Target server type text box
3. Under Select source enter the following and click **Next**:
    * Server name: **SQLVM** 
    * Authentication type: **SQL Server Authentication**
    * In the **Username** box, type `Goose`
    * In the **Password** box enter `Complex.Password`
    * Click **Connect**.
    * Uncheck **Assess database before migration?** and click **Next**.
4. On the **Select target** tab, enter the following:
    * Specify the source connection details for your SQL Server.  This is the FQDN name of the Azure SQL Database.
    * Authentication type: **SQL Server Authentication**
    * In the **Username** box, type `Goose`
    * In the **Password** box, type `Complex.Password`
    * Uncheck **Encryption connection**.
    * Click **Connect**.
5. Select **Cloud** when it appears and click **Next**.
6. On the **Select Objects tab** click **Generate SQL Script**.
7. On the **Script & deploy schema** tab, review the script and click **Deploy schema**.  Once complete, and that task should be fast, click **Migrate Data**.
8. On the **Select tables** tab, ensure that the row count is 38,803 and click **Start data migration**.
9. The migration should take less than 30 seconds.  Review the tab for deployment information on warnings, failures, etc.  

### **Congratulations, you have just migrated your first database to the cloud!**

## Exercise 10 - Validate your data in SQL

Let's make sure that your data got migrated and looks right.  We're going to connect to the Azure SQL database and example the data.

1. Switch to the **Microsoft SQL Server Management Studio** application.
2. Click on **File** then **Connect Object Explorer**.
3. Enter the following in the Connect to Server form and click **Connect**:
    * Server name: Enter your (Azure) SQL Server name. (e.g. `abc032019.database.windows.net`)
    * Authentication type: SQL Server Authentication
    * In the Username box, type `Goose`
    * In the Password box, type `Complex.Password`
4. Browse databases to select **Cloud**.
5. Right-click on **New Query** and enter the following:
    * `select TOP (1000) [zip] FROM Cloud.dbo.sampledata`
6. Click **Execute**.  This query will search the Azure SQL database and display the first 1000 zip codes.

### **Congratulations for completing the SQL Migration lab!**

<br></br>
[Back to Table of Contents](./index.md#4-azure-migrate)