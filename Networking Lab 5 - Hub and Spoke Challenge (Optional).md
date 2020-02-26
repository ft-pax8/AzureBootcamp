# Hub and Spoke Networking Challenge

Now that you have vNet1 and vNet2 peered, resources in each virtual network and subnet can communicate with each other.  A common networking architecture is a hub-spoke network topology where the hub is a single virtual network (VNet) in Azure that acts as a central point of connectivity to your virtual networks (VNets). The spokes are VNets that peer with the hub, and can be used to isolate workloads.

Your challenge is to configure vNet2 as the hub in a hub and spoke topology.  vNet1 and vNet3 are not directly connected together.  vNet1 talks to vNet3 via vNet2 and vNet3 talks to vNet1 via vNet2.

**Note:** One of the resources you need to provision in order to complete this lab could take up to 45 mins. to install.  Please make sure you have enough time to complete this lab should you choose to take on the challenge.

## Exercise 1 - Create the third VM

We are going to create the third VM using PowerShell within Cloud Shell.  

1. Click on the Cloud Shell icon on the taskbar: **>_**
2. Select **PowerShell**.
3. If you are prompted, select **Create Storage**.
4. Create vNet3 by running the following CLI command:
`az network vnet create --name vNet3 --resource-group vnets --address-prefixes 10.103.0.0/16 --location westus2`

5. Create subnet3 by running the following CLI command:
`az network vnet subnet create --address-prefix 10.103.3.0/24 --name subnet3 --resource-group vnets --vnet-name vNet3`

6. Enter the following to set the username and password needed for the administrator account on the VM :
    `$cred = Get-Credential`
7. Enter `Goose` as the user
8. Enter `Complex.Password` as the password
9. Create the VM (note to use the correct region):
    `New-AzVm
    -ResourceGroupName "Vnets"
    -Name "VM3"
    -Location "WestUS2"
    -VirtualNetworkName "vNet3"
    -SubnetName "Subnet3"
    -SecurityGroupName "VM3-nsg"
    -PublicIpAddressName "VM3-ip"
    -Credential $cred
    -size Standard_D2_v2`
10.  Login to VM3 and add the firewall rule to allow ICMPv4 *(see exercise 1)*


## Exercise 2 - Create the Hub and Spoke

Attempt to configure this on your own, otherwise follow these instructions.  You should configure spokes to use the hub VNet gateway to communicate with remote networks. To allow gateway traffic to flow from spoke to hub, and connect to remote networks, you must:

* Configure the VNet peering connection in the hub to allow gateway transit.
* Configure the VNet peering connection in each spoke to use remote gateways.
* Configure all VNet peering connections to allow forwarded traffic.  

Reference [Hub-spoke network topology in Azure](<https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke>) for guidance.

<br></br>
[Back to Table of Contents](./index.md#3-networking)