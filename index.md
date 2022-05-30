## Azure DNS Private Resolver

Microsoft has released new Azure DNS Private Resolver service to simplify hybrid recursive DNS resolution. This reference architecture demonstrates how a hybrid Domain Name System (DNS) solution can be designed using Microsoft Azure Private DNS Resolver for workloads running both on-premises and in Microsoft Azure.

Azure offers various types of DNS solutions, as described below:

Azure offers various types of DNS solutions, as described below:
- Azure Public DNS: This is the default DNS resolution for all Azure virtual networks and it is maintained and managed by Microsoft.

- Azure Traffic manager: Acts as a DNS-based load balancing service, allowing us to distribute traffic across Azure regions to the public facing applications.

- Private DNS: You can use Azure DNS private zones to resolve your own domain names and virtual machine names without having to configure a custom solution without modifying your own configuration. When deploying, you can also use your own custom domain name instead of Azure's provided names if you use private DNS zones.

- Azure DNS Resolver (Preview): is a cloud-native, highly available, and DevOps-friendly service. It provides a simple, zero-maintenance, reliable, and secure DNS service to resolve DNS names hosted in Azure DNS private zones 



### Why Azure DNS Private Resolver:
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




You can use the [editor on GitHub](https://github.com/moorthyannadurai/AzureDNSPrivateResolver/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/moorthyannadurai/AzureDNSPrivateResolver/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
