# ADLS Gen1 in a vNet

The goal is to limit access to ADLS Gen1 only through a VNet using VNet Service Endpoints. 
Configure VNet Service Endpoints for ADLS Gen1 per the documentation (selecting VNet Service Endpoints for AAD in the VNet, obtaining the IP of their ADLS instance, configuring the UDR and configuring the ADLS Gen1 firewall to allow communication from the VNet).
VM1 has a public IP, it is part of the vNet. VM2 does not have a public IP and can only be accessed from VM1. Inside VM2 I create a self hosted Integration runtime for Data factory. NSG has an opening for my computer to RDP to VM1.

![adls-vnet-visio](/adls-vnet-visio.png?raw=true "Visio")

When accessing ADLS from ADF or Storage explorer through VM2 there are two options to get access: 
Option 1) 
Allow all Azure services to access this Data Lake Storage Gen1 account.
Option 2)
Open the ADLS fw to VM2 IP (managed by Azure, not static).
 
![alt text](https://docs.microsoft.com/en-us/azure/data-lake-store/media/data-lake-store-network-security/firewall-exceptions.png)
 
Option 2 is not a viable option. You won’t be able to maintain the FW, the VM could be assigned any IP in the DC’s available range. I wanted to highlight this to show the PG feedback we gave. When you explicitly allow that public IP (which is the public IP used for default outbound NAT connections from the VNet) in the ADLS Gen1 Firewall settings, then the connection from Azure Data Factory is successful. https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json
 
In the documentation, under the exceptions section, and it states that customers must select the “Allow all Azure services to access this Data Lake Storage Gen 1 account” checkbox is they need Azure services, including Azure Data Factory, to access the ADLS instance. 
