# Lab 2 - Storage: VM and Disk Sizing


## Before you Begin

Please ensure you completed Lab 1 - Storage: Creating Data Disks, as you will use the same VM and disks to complete this lab.

## Lab Concepts

Both Azure VMs and Disks have their own performance metrics, specifically IOPS and throughput.  As such, the VM you provision has an effect on disk performance and vice-versa.  To ensure your application is as performant as possible, both need to be sized appropriately.  In this lab you will see this dependency in action and how to identify a performance bottle-neck.  You will use IOMeter to generate load on your VM and disks and then analyze the results to see if you're getting the performance you should be getting.  Finally, you will learn what additional settings may affect performance and how to detect and resolve them.

After completing this lab, you should understand how to:
1. Size a VM and Disks for best application performance
2. Simulate load on VM Disks for testing purposes
3. Analyze performance issues 
4. Identify the best disk configuration for your application. 


<br></br>
Use this performance tables below to help you with the analysis questions in the lab exercises:


### Azure Storage Performance

| Storage Type          |  Max IOPS (per disk)  | Max Throughput (per disk) |
|-----------------------|-----------------------|-----------------|
| P10 - Premium Storage |  500                  | 100  MB/sec     |                       
| P20 - Premium Storage |  2,300                | 150 MB/sec      |
| P40 - Premium Storage |  7,500                | 250 MB/sec      | 


### Azure Compute Performance

| Storage Type          |  Max IOPS (w/caching) | Max Throughput (w/caching)  |  Max IOPS (w/o caching) | Max Throughput (w/o caching)  |
|-----------------------|-----------------------|-----------------------------|-------------------------|-------------------------------|
| DS1_v2                |  4,000                | 32  MB/sec                  | 3,200                   | 48     			|
| DS3_v2                |  16,000               | 128 MB/sec                  |	12,800			| 192				|


<br></br>

## Exercise 1 - Test Azure Disk Performance


1. RDP into **VMSTOR1**
2. Open Server Maanger and turn off *IE Enhanced Security Configuration*
3. Open a web browser and download the [IOMeter Tool](https://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download)
4. Install the tool, accepting all defaults.
5. Launch IOMeter and accept the EULA
6. Within IOMeter, click on VMSTOR1 then Worker1
7. Click on the **M: AppData** drive, a red X should appear next to it
8. Change the **Maximum Disk Size** sectors to `1000000` (which is a 1 & 6 zero's) and the **# of Outstanding I/Os** to `100`
9. Click on the **Access Specifications** tab
10. Select `32K; 100% Read; 0% Random` and then click **add**
11. Click the green flag at the top menu to *start tests*
12. Save the results.csv file to the Documents folder
13. IOMeter will warm up the drive by writing to the max sectors, then perform the IO Read using 32KB payloads.
14. Click on the **Results Display** tab and then change the *Update Frequency* to **5** to have it refresh the results every 5 seconds, and change the **Results Since** to `Last Update` so we can remove the outliers during the warming up period
15. Note the **Total I/Os per Second**, **Total MBs per Second** and **Maximum I/O Response Time (ms)**.  Write the values down (they will fluctuate, but just write down the last seen numbers).
16. Let this run for at least 3-5 mins to ensure you get good sample data
17. After 3-5 mins, click **Stop** on the top menu
18. Select the **Disk Targets** tab and uncheck **M: AppData** and check **L: Logs**
19. Repeat steps 11-17
20. How do the results compare between the test run on the M drive versus the L drive?  Are the pretty close?  Why is this if drive M is a P10 and L is a P40?
21. From **VMSTOR1**, search for `Resource Manager` from the search bar and open the program.
22. Click on the **Disk** tab and expand the **storage** blade 
23. Re-run the step 11-17 again for either the M: or L: and watch the disk metrics in Reesource Monitor for pointers.  *Hint: pay close attention to the throughput of total disk and the queue length of the disk IOMeter is running the test on.*
24. What conclusions can you draw from this data and using the tables above?  Why do both the P10 and P40 disks perform relatively the same, when the P40 disk has more than double the IOPs and throughput?


## Exercise 2 - Maximize Storage Performance
1. By now, you may have identified that the VM size is limiting the performance of read/write operations on the disk because a DS1_v2 only has 32 MB/sec of throughput.
2. You will now scale-up the VM to see if we can improve the performance of the existing attached disks.
3. Browse to **VMSTOR1** within the Azure Portal
4. Click on **Size** under the *Settings* section
5. Change the size of VMSTOR1 to a **DS3_v2**  *Note: If you are using a free subscription, you must have deleted the VMs from all other labs to have enough core quota available.  If a DS3_v2 size is not available, ensure you have deleted all other VMs in your free subscription.*
6. Click **Resize**
7. Wait for the VM to resize and then restart.  Then remote back in to VMSTOR1.
8. Open Resource Monitor on VMSTOR1 and then repeat steps 11-17 once again, for both the L: and M: drives.  This time, monitor the performance using Resource Monitor while you are performing the load tests.  Once again, make sure to wait 3-5 minutes for the disks to warm up before making the assessment.
9. Did the performance improve?  Is there any variation between the L: (P40) and M: (P10) drive?  How many IOPs is the M: (P10) disk receiving?  Is this number greater or lower than the stated Max IOPs for that size disk?  What could contribute to this?


## Exercise 3 - Putting it all together
Let's summerize what you should have been able to observe from the first 2 exercises.  From the first exercise, you should have noticed that the disks were experiencing performance issues, observed by the size of their queue length.  This indicates that the disks could not keep up with the I/O requests.  You should have also noticed that the max throughput or **Total MBs per Second** was capping around 31 MB, after the disks warmed up, even though both disks are capable of handling more.  This coincides with the max throughput of a DS1_v2 of 32 MB/sec.  

After scaling-up the VM in exercise 2 for a VM size with greater IOPs and throughput, you should have noticed performance increased for both disks.  However, both still saw high queue lengths and max throughput was capped around 128 MB.  Once again, this coincides with the max throughput of the VM - a DS3_v2 in this case.  We now know that VM throughput was preventing us from maximizing disk performance in both exercises.  Moving to a VM with a max throughput >= to the max throughput of a disk would help us achieve more optimal performance.  

Did you notice any other weird behavior?  In both exercises, both disks performed similar even through the P40 disk has greater IOPs and throughput than the P10.  Additionally, you should have noticed that the M: (P10) disk was able to handle IOPs of 4K+, which is several thousand greater than its quoted max of 500.  What could attribute to this?  Let's find out....

1. In the Azure portal, go to VM *VMSTOR01**
2. Click on the **Disks** menu item under *Settings*
3. Click *Edit*
4. Change **Host Caching** to `None` for all 4 disks
5. Remote into VMSTOR1 and repeat steps 11-17 once again, for both L: and M:
6. Did you receive the same IOPs and throughput once you removed caching?  
7. Play around with the **Global Access Specifications** under the *Access Specifications* tab using 512B, 4K, 16K, 32K or even a custom value to see how it impacts the results of the test.  You will have to stop and start IOMeter each time you would like to change the specification.
8. Do the same within the Azure portal by turning on and off **Host Caching** to see how it impacts the performance
9. Lastly, perform a final run on the S: drive.  You should see very similar results as you do when you run on the M: drive as long as all else is equal?  Why do the two P6 disks perform as well as the P10 disk?

You may have noticed that in exercise 1, the M: drive was limted to 300-500 IOPs for the first minute and then sped up to 3000+.  This is because IOMeter is reading the same data over and over again, and after a short time period, the VM put the data in cache.  IOMeter was then actually making reads against the cache, not the disks.  This, plus the ability to go over the disk's max IOPs were indicators that caching was taking place.  

As you can see, not only does VM and disk sizing play an important role in optimizing disk - and ultimately app performance, but so does I/O size, host caching and networking (we didn't touch on accelerated networking in this lab).  These items are often overlooked and misunderstood, but hopefully you now have a good understanding of how to architect for optimal disk performance. 

<br></br>
To learn more about optimizing disk performance for Azure disks, visit the [Azure premium storage: design for high performance](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/premium-storage-performance) page.

<br></br>
[Back to Table of Contents](./index.md#5-azure-storage)