
# Lab 3 - Network Watcher

## Before you Begin

You will use the VMs from Lab 3.  Do not delete the load balancer lab VMs until after you have completed this lab.  

**Prior to starting this lab, please make sure LBVM1 is started and LBVM2 is in a stopped (deallocated) state.  If their states differ, please start/stop the VMs to place them in the correct state before starting the lab.**


## Lab Concepts

There are often times when applications or systems will experience connectivity issues whether it be latency, intermittent timeouts or failed connections.  With Hybrid workloads, it's often difficult to identify if the issue is stemming from the on-premises network, Azure or the internet/customer's systems.  Network packet captures help us look at the traffic coming in and out (ingress/egress) of a particular VM.  This can help you identify if and where the issue is by analyzing the packet capture (pcap).  Since all networking is virtualized, we don't need to install packet capture software directly on to a VM to run the PCAP.  Instead, we can run a packet capture right from an Azure service - Network Watcher.

After completing this lab, you should understand how to:
1. Use Azure Network Watcher to create a packet capture on an Azure VM.
2. Download and view a PCAP taken by Network Watcher


To test network communication with Network Watcher, first enable a network watcher in at least one Azure region, and then use Network Watcher's IP flow verify capability.

## Enable network watcher

1. In the portal, select **All services**. In the Filter box, enter **Network Watcher**. When Network Watcher appears in the results, select it.
1. Enable network watcher by clicking on the elipsys **...**.

## Packet Capture and examination

1. From **Network Watcher** under **Network diagnostic tools**, select **Packet Capture**, then **Add**.
2. Enter the following and click **Ok**:
   - Resource Group: **LoadBalVMs**
   - Target VM: LBVM1
   - Packet Capture Name: **Packets**
   - Maximum bytes per packet: **0**
   - Maximum bytes per session: **1073741824**
   - Time limit: **18000**
3. Click OK to run the packet capture

*Note that it may take several minutes for the packet capture to initialize.  If you get an error, ensure that LBVM1 is running.*

1. Find the public IP address for the load balancer **LB01** which should be **LBPublicIP**.  Paste the public IP address of your Load Balancer into the address bar of your browser and notice the default page of the IIS web server is displayed in the browser
2. Hit refresh a few times to generate additional traffic to the website and VM.
3. Stop the packet capture by returning to **Network Watcher** > **Packet Capture** >**Right-Click** > **Stop**.
4. Click on the .cap file under the blob properties near the bottom of the screen.  Download the file to your documents folder on your local computer.
5. Install network monitor from the following: <https://www.microsoft.com/en-US/download/details.aspx?id=4865> or you may use your own software that is capable of reading pcap files.
6. Launch Network Monitor and open the capture file from your Documents folder.  Examine the contents of your packet capture session.

<br></br>
[Back to Table of Contents](./index.md#3-networking)