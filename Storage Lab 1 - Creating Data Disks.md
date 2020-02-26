# Lab 1 - Storage: Creating Data Disks


## Before you Begin

Your Azure subscription is limited in the amount of cores that you can provision.  Ensure that you have deleted the VMs from the previous labs before starting this lab. 


## Lab Concepts

In this lab you will create a VM and attach multiple data disks to it.  Data disks allow you to create new volumes on the server for permanent application and data storage.  They can be simple volumes, or dynamic disks offering striped and mirrored capabilities.  


After completing this lab, you should understand how to:
1. Create new data disks and assign them to a VM
2. Format and mount the data disks on the VM
3. Create a striped (Raid 0) volume using 2 or more data disks



## Exercise 1 - Create a VM 


1. On the Azure portal menu or from the Home page, select **Create a resource**.
2. *Windows Server 2016 VM* should be in the list of Popular Marketplace elements. If not, try searching for "Windows Server 2016 DataCenter" using the search box on the top.
3. Select the Windows VM and click **Create** to start the VM creation process.
4. Under Resource Group, select new and enter **storagePerf**
5. In the Virtual machine name box, enter **VMSTOR1**
6. In the Location drop-down list, select a consistent region for your resource.
7. For the VM Size, select **DS1 v2**
8. In ADMINISTRATOR ACCOUNT section, enter `goose` for Username and `Complex.Password` for password
9. Leave the defaults for the remaining tabs and fields and click **Review + create.**
10. Click **Create** after successful validation


## Exercise 2 - Attach New Data Disks
Once your VM is created, you will add the data disks. Data disks can be created at the same time as VM creation, or afterward, such as this case.

1. Search for **VMSTOR1** and click on the resource to go to the overview page
2. Under 'Settings', click **Disks**
3. Click **+ Add data disk**
4. Set the LUN to **0**
5. Under Name, select the drop down and click **create disk**
6. Disk Name = **appdata**
7. Resource Group = **storagePerf**
8. Source Type = **None**
9. Click **Change Size**
10. Select **P10** and then **OK**
11. Click **Create**
12. Add a 2nd data disk repeating steps 3-11, with values of LUN=**1**, disk name=**logs** and size=**P40**
13. Add a 3rd data disk repeating steps 3-11, with values of LUN=**2**, disk name=**sqldisk1** and size=**P6**
14. Add a 4th data disk repeating steps 3-11, with values of LUN=**3**, disk name=**sqldisk2** and size=**P6**
15. Leave 'Host Caching' set to **Read-only** for all new disks
16. Click **Save**


## Exercise 3 - Mount the Data Disks as new Volumes
Before you VM can use the data disks you just attached, they must first be mounted, formatted and assigned to a volume.  Let's add 2 standard volumes and 1 striped volume.

1. RDP into **VMSTOR1**
2. Click **No** when the Networks discovery prompt comes up
3. Right-click on the start menu and select **Disk Management**
4. If you are prompted to *Initialize Disk*, ensure that all disks are selected, select **MBR (Master Boot Record), click **OK**
5. Once the disks are initialized, you need to format them and assign them a drive letter.
6. Right-click **Disk 2** or the disk with *128GB* of unallocated space, and select **New Simple Volume**
7. Click Next twice
8. Assign Drive Letter **M** and click Next
9. Select **NTFS** as the file system format and check **Perform a quick format**
10. Enter **AppData** as the volume name and click Next
11. Click Finish
12. Repeat steps 6-11 for disk 3 or the disk with 2TB of unallocated space and use drive letter=**L** and Volume Name=**Logs**
13. Now you will create a new stiped volume
14. Right-click Disk 4 or one of the disks with 64GB of unallocated space and select **New Striped Volume**
15. Click **Next**
16. Under *Available* disks, select disk 5 or the other 64GB disk and click **Add**.  Both drives should now be listed under the *Selected* area.
17. Click **Next**
18. Assign Drive Letter **S** and click **Next**
19. Select **NTFS** as the file system format and check **Perform a quick format**
20. Enter **SQL** as the volume name and click Next
21. Click **Finish**.  If you receive a prompt to convert the disks from basic disks to dynamic disks, click **OK**
22. Make sure all formating completes successfully and all disks are listed as Healthy.
24. Open a File Explorer window and make sure you can access the new volumes.

**Congratulations, you just created a VM with multiple data disks and a striped volume**


<br></br>
[Back to Table of Contents](./index.md#5-azure-storage)