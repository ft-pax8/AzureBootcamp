# Lab 3 - Storage: Azure Files

## Before you Begin

There are no prerequisites for this lab.

## Lab Concepts
If users need to preseve a file structure where they can browse and modify files, Azure Files provides this capability. In this lab you will learn how to create Azure file shares that users can connect to.  You will map a drive to the new share and also create snapshots of files to protect them from accidentaly modification.  Lastly, you will use Azure Storage Explorer to see how easy it is to interact with Azure storage.


After completing this lab, you should understand how to:
1. Create an Azure File Share
1. Map a drive to an Azure File Share
1. Create a File Share Snapshot
1. Use Azure Storage Explorer to work with Azure Storage Accounts



## Exercise 1 - Create a New Azure File Share

1. From the Azure Portal, select **+ Create a Resource**
1. Click **Storage** > **Storage account - blob, file, table, queue**
1. Resource Group - click *New* and name is **fileshare**
1. Storage Account = `safiles<first name><last 4 of cell>` *(i.e. safilesjoe1234)*
1. Location = **West US 2**
1. Account kind = **StorageV2 (general purpose v2)**
1. Replication = **Locally-redundant storage (LRS)**
1. Access Tier = **Hot**
1. Click **Review + create**
1. Click **Create** after the successful validation
1. Wait for the deployment to complete and then click on **go to resource**
1. Click on **File Shares** on the menu under *File Service* 
1. Click **+ File Share**
1. Name = **myfiles**
1. Quota = **2 GiB**
1. Click **Create**


## Exercise 2 - Connect to the File Share
There are multiple ways to connect to a file share - SMB, FTPS, NFS, API.  In this exercise, you will connect to your new fileshare using SMB via a Windows UNC/drive mapping.

1. In the Azure portal, browse to your new storage account, file shares and then click on the new file share **myfiles**
1. Click **Connect** on the upper menu
1. You will be presented with a pre-configured script for your specific storage account.  You will run this script on the laptop you are running the lab from.  Click on the appropriate OS of your laptop and copy the script.
1. For Windows, open a PowerShell window - Linux = Command line, Max = Terminal.  If you don't want to do this on your own machine or your machine blocks port 445, use VMSTOR1 from storage Lab 1 
1. Paste the script into the appropriate window and run it
1. Look at the script you pasted in, specifically look at the password.  It should be a long string of alphanumeric and special characters.  This is your storage account access key.  
1. Within the Azure Portal, click on **Access Keys** underneath the storage accounts `Settings` menu and verify that it matches the password in the script.  This key is what allows us to authenticate to the storage account file share since it does not support AAD or ADDS authentication.
1. Once the Z: maps to the fileshare, browse to the Z: drive and verify that you can successfully access it.
1. Upload a file to the Z:, then go back to the Azure portal and click **Refresh** on `myfiles` to see the file you uploaded 
1. Click **+ Add Directory** and give the new directory a name and click **OK**
1. Switch back to the Z: and verify the new directory appears


## Exercise 3 - File Versioning
Azure Files provides the capability to take share snapshots of file shares. Share snapshots capture the share state at that point in time.  This helps prevent against data corruption, accidental deletes/changes and also provides general backup.  Let's see how easy it is to implement.

1. On your local machine, create two txt files named File1.txt and File2.txt
1. Add some text to each file (at least 5 words)
1. Save the text files and upload them to the Z:
1. In the Azure portal, open **myfiles** and browse to the **Snapshots** menu item
1. Generate a new snapshot by clicking **+ Add Snapshot**
1. Wait for the snapshot to complete then go back to your Z:
1. Delete File1.txt
1. Open File2.txt and overwrite the text in the file with new text.  Save File2.txt
1. Go back to the Azure portal and click **Overview** and then **Refresh**
1. Verify File1.txt is deleted.  Open File2.txt and verify your text was overwritten succesfully and then close File2.txt
1. Now that we know the delete and modify have been applied, let's restore them using the snapshot
1. In the Azure Portal, click **Snapshots** and click on the snapshot you created
1. Click File1.txt and then **Restore** in the new window
1. Select **Overwrite original file** and click **ok**
1. Now click on File2.txt and click **Restore**
1. Select **Restore as a copy and rename** and rename the file to File2-restore.txt and click **OK**
1. Go back to the Z: and verify File1.txt was restored and that you see the original File2.txt (now called File2-restore.txt).  Open File2-restore.txt to ensure the text matches the text you previously entered.

You can create snapshots manually, or automatically using Azure Functions or Azure Automation.  The maximum number of share snapshots that Azure Files allows today is 200. After 200 share snapshots, you have to delete older share snapshots in order to create new ones. Share snapshots are incremental in nature. Only the data that has changed after your most recent share snapshot is saved. This minimizes the time required to create the share snapshot and saves on storage costs. 


## Exercise 4 - Explore the File Share using Azure File Explorer
Azure File Explorer is client that you can install on your machine to read/write data to Azure Blob accounts.  It allows you to access the storage accounts using your Microsoft Account or your Storage's SAS Key or Access Token.

1. If you don't have Azure Storage Explorer installed, browse to [https://azure.microsoft.com/en-us/features/storage-explorer/](https://azure.microsoft.com/en-us/features/storage-explorer/) to download and install it now.
1. Once installed, open Azure Storage Explorer
1. Click on the person icon on the left menu, **Manage Accounts**
1. Login with your Microsoft account
1. Once authenticated, browse to the storage account you created in exercise 1
1. Expand the storage account and notice that you can see blob, queue, table and file shares
1. Click on **File Shares** and then **MyFiles**
1. You should see the files and directory you created in Exercise 3. 
1. Browse the toolbar and the options available to you using Azure Storage Explorer, including the ability to browse snapshots.  However, note that you cannot upload to the file share as it does not support SMB
1. Now right-click on **Blob Containers** and select **Create Blob Container** and name it `myblob`
1. Select **myblob**
1. Notice that now you have the ability to upload files directly to blob storge.  Click **upload** and **upload files** to upload a new file to the blob container
1. Once uploaded, look in the bottom window.  You should see a message indicating the transfer is complete and then a `Copy AzCopy Command to Clipboard' click on this link.
1. On your own machine, open a document editor and paste the AzCopy command you just copied. This is an easy way to have the AZCopy syntax created automatically for you.  You can then modify select portions to suit your specific use case.

>AzCopy is a command-line utility that you can use to copy blobs or files to or from a storage account. AZCopy is the recommended tool to upload/migrate on-premises data to Azure.  It is optimized to make this data transfer and often the fastest possible option.

You can download the free utility at [https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10)

15. Lastly, in Azure Storage Explorer, click on **Disks** and then **storagePerf** resource group.
16. You should see the disks you created in Storage Lab 1. 
17. Select one and notice that you can download it as a VHD or even create a new snapshot of the disk.

The takeaway here is that Azure Storage  is a standalone app that makes it easy to work with Azure Storage data on Windows, macOS, and Linux platforms.



<br></br>
[Back to Table of Contents](./index.md#5-azure-storage)