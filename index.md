# Azure DNS Private Resolver

Microsoft has released new Azure DNS Private Resolver service to simplify hybrid recursive DNS resolution. This reference architecture demonstrates how a hybrid Domain Name System (DNS) solution can be designed using Microsoft Azure Private DNS Resolver for workloads running both on-premises and in Microsoft Azure.

Azure offers various types of DNS solutions, as described below:

- __Azure Public DNS:__ This is the default DNS resolution for all Azure virtual networks and it is maintained and managed by Microsoft.

- __Azure Traffic manager:__ Acts as a DNS-based load balancing service, allowing us to distribute traffic across Azure regions to the public facing applications.

- __Azure Private DNS:__ You can use Azure DNS private zones to resolve your own domain names and virtual machine names without having to configure a custom solution without modifying your own configuration. When deploying, you can also use your own custom domain name instead of Azure's provided names if you use private DNS zones.

- __Azure DNS Resolver (Preview):__ is a cloud-native, highly available, and DevOps-friendly service. It provides a simple, zero-maintenance, reliable, and secure DNS service to resolve DNS names hosted in Azure DNS private zones 



# Why Azure DNS Private Resolver:

Prior to Azure DNS Private Resolver, customers must set up custom DNS servers to enable DNS resolution from on-premises to Azure (and vice versa). Below are the typical challenges associated with custom DNS solutions.

- Managing multiple custom DNS servers for multiple virtual networks requires high infrastructure and licensing costs.
- The customer must handle all aspects of installing, configuring, and maintaining DNS servers.
- Overheads such as monitoring, and patching of these servers become complex and prone to failure.
- There is no DevOps support for customers to manage their DNS records and forwarding rules
- Costs associated with implementing scalable customer DNS server solutions are high.



Here are the features and key advantages that Azure DNS Private Resolver brings to the table.

- A fully managed Microsoft service with high availability and zone redundancy built in for better availability and service reliability.
- Very scalable and friendly to DevOps.
- A cost saving when compared with traditional IaaS-based custom solutions.
- The Outbound Endpoint brings conditional forwarding capability for Azure DNS to on-premises that has never been possible before.


# Architecture
The following diagram illustrates how a hybrid network looks using Azure DNS Private Resolver:

![DPR Architecture](https://raw.githubusercontent.com/moorthyannadurai/AzureDNSPrivateResolver/gh-pages/Slide1.JPG)


# Components
Following are the components that comprise the architecture:

## On-premises network. 

On-premises networks represent customer datacenters that are connected to Azure via either ExpressRoute or S2S VPN. The components of the on-premises network in this use case are as follows:

- __Local DNS servers:__ Both of these servers (192.168.0.1 and 192.168.0.2) have the DNS service installed and serve as resolvers/forwarders for all the computers inside the on-premises network.

Note: The administrator must create all Local DNS and Azure endpoints on these servers. For this scenario, On-premises DNS servers have conditional forwarders configured for the blob and API private endpoint DNS zones, pointing to the Azure DNS Private Resolver Inbound connection (IP: 10.0.0.8) hosted within the hub VNet.

- App1.onprem.company.com - 192.168.0.8
- App2.onprem.company.com - 192.168.0.9
- privatelink.blob.core.windows.net – 10.0.0.8 (DNS Forwarder record)
- privatelink.azure-api.net – 10.0.0.8 (DNS Forwarder record)
	
	
## Hub Network.

- __Gateway:__ The hybrid connection to Azure is formed either by a VPN device or by an ExpressRoute connection.

- The following parameters are configured for Azure DNS Private Resolver. adsfsdfdsf

__Hub Virtual Network:__
- vNET: 10.0.0.0/24

__Inbound Endpoint:__

- Subnet: 10.0.0.0/28
- IP: 10.0.0.8

__Outbound Endpoint:__

- Subnet: 10.0.0.16/28
- IP: 10.0.0.19
- For App1 and App2 DNS names, the DNS forwarding rule set is configured


- The hub VNet linked to the Private DNS zone names for Azure services (such as privatelink.blob.core.windows.net, as shown in the picture).

## Spoke Networks.
- Virtual machines are hosted in all spoke networks in order to test and validate DNS resolution
- All Azure spoke VNets use the default Azure provided DNS (192.63.129.16) and peered with hub VNet.
- As shown in the picture, these spoke VNets are linked to Private DNS zone names to resolve private endpoint link services (such as privatelink.blob.core.windows.net).


# Traffic Flow

The following diagram shows the traffic flow when on-premises server triggers a DNS query.
 
 ![DPR Architecture](https://raw.githubusercontent.com/moorthyannadurai/AzureDNSPrivateResolver/gh-pages/Slide2.JPG)
 
- On Prem servers query Azure Private DNS records (ex. abc.privatelink.blob.core.windows.net).
- DNS query requests are sent to local DNS server (192.168.0.1 / 2) (all on-premises computers point to local DNS server (192.168.0.1 / 2))
- Because of conditional forwarding on the local DNS server for privatelink.blob.core.windows.net, the request is being sent to Private DNS Resolver Inbound Endpoint IP 10.0.0.8.
- Azure Private DNS resolves DNS queries sent to DNS Resolvers' inbound endpoint through Azure Public DNS.


The following diagram shows the traffic flow when VM 1 triggers a DNS query. In this case, a spoke network 1 attemp to resolve DNS.

![DPR Architecture](https://raw.githubusercontent.com/moorthyannadurai/AzureDNSPrivateResolver/gh-pages/Slide3.JPG)

- One of the VMs (VM 1) queries a DNS record.
- Since Azure Provided DNS is configured on the spoke VNets, any DNS queries from the spokes will be directed to Azure Public DNS.
- Azure Private DNS is contacted if a query is made for name resolution
- Else, Azure DNS connects to Azure DNS Private Resolver to check for DNS Forwarding Rules associated with spoke 1 vnet.
- If yes, the DNS query is scanned through DNS Forwarding Rules for a matching record, then it is forwarded (via Outbound Endpoint) to the IP address stated as part of the matching rule for DNS resolution.
- If No, Azure Public DNS is contacted if results are not found.
                
                
Note: Each DNS forwarding rule specifies one or more target DNS servers that are to be used for conditional forwarding, including the Domain Name, Target IP, and Port.



# Considerations

The following points should be taken into account when implementing this solution:

## Planning

The following resources provide more information about creating a private DNS resolver:
- Create an [Azure DNS Private Resolver using the Azure portal](https://docs.microsoft.com/en-us/azure/dns/dns-private-resolver-get-started-portal)
- Create an [Azure DNS Private Resolver using Azure PowerShell](https://docs.microsoft.com/en-us/azure/dns/dns-private-resolver-get-started-powershell)




## Scalability

Azure DNS Private Resolver is a fully managed Microsoft service that can handle millions of requests. The minimum subnet address space must be /28, up to /24. So do set the size accordingly (/26 is a sweet spot for most customers).


## Guidelines

Following are some guidelines for virtual networks:

- Azure DNS Private resolvers can only resolve virtual networks within the same geographical region.
- A virtual network cannot contain more than one DNS resolver. There can only be one resolver per virtual network.
- Dedicated subnets should be assigned to each of the inbound and outbound endpoints. 

## Regional Availability

Azure DNS Private Resolver service is available in the regions listed [here](https://docs.microsoft.com/en-us/azure/dns/dns-private-resolver-overview#regional-availability). 
Note: A DNS resolver can only refer to a virtual network in the same region as the DNS resolver.

## Security

Azure DNS supports the extended ASCII encoding set for TXT record sets. Further information is available here.

Domain Name System Security Extensions are not currently supported by Azure DNS. This is one of the feature requests on the new feature request [page](https://docs.microsoft.com/en-us/azure/dns/dns-faq#does-azure-dns-support-the-extended-ascii-encoding--8-bit--set-for-txt-record-sets-).

## Pricing 

- As a solution, Azure DNS Private Resolver is largely cost-effective. One of the primary benefits of Azure DNS Private Resolver is it is fully managed, eliminating the need for dedicated servers. 

- Calculate the cost of Azure DNS Private Resolver using the [Azure pricing calculator](https://azure.microsoft.com/pricing/calculator/). Pricing models for Azure DNS Private Resolver are explained [here](https://azure.microsoft.com/en-in/pricing/details/dns/). 

- Pricing also includes availability and scalability features. 

- Azure [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/) supports two billing models: metered data, which charges you per GB for data transfer outbound, or unlimited data, which includes all data transfer. 

- When using [VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) instead of ExpressRoute, the cost varies by the SKU and is charged per hour. 

## Reverse DNS Support

Overview of how reverse DNS works, and scenarios in which Azure DNS supports , are provided in this [article.](https://docs.microsoft.com/en-us/azure/dns/dns-reverse-dns-overview)

Traditionally, DNS records map a DNS name to an IP address, for example www.contoso.com resolves to 42.3.10.170. Reverse DNS is the opposite, where an IP address is mapped back to a name. For example, a lookup of 42.3.10.170 resolves to www.contoso.com.


# Next steps


[What is a virtual network link?](https://docs.microsoft.com/en-us/azure/dns/private-dns-virtual-network-links)

[What is Azure DNS?](https://docs.microsoft.com/en-us/azure/dns/dns-overview)

[What is Azure Private DNS?](https://docs.microsoft.com/en-us/azure/dns/private-dns-overview)

[What is Azure DNS Private Resolver?](https://docs.microsoft.com/en-us/azure/dns/dns-private-resolver-overview)

[zure DNS FAQ](https://docs.microsoft.com/en-us/azure/dns/dns-faq)

[Overview of reverse DNS](https://docs.microsoft.com/en-us/azure/dns/dns-reverse-dns-overview)

