<properties
	pageTitle="Azure SQL Database (DBaaS) vs. SQL Server on Azure VMs | Microsoft Azure"
	description="Learn which cloud SQL Server option fits your application: Azure SQL Database (DBaaS) or SQL Server on Azure Virtual Machines."
	services="sql-database, virtual-machines"
	keywords="SQL Server cloud, SQL Server in the cloud, SaaS database, cloud SQL Server, DBaaS"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor="cjgronlund"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/16/2015"
	ms.author="jeffreyg"/>

# Selecting a SQL Server option in Azure: Azure SQL Database (DBaaS) or SQL Server on Azure VMs (IaaS)

Microsoft Azure has two options for hosting SQL Server workloads:

* [Azure SQL Database](https://azure.microsoft.com/services/sql-database/): A Database-as-a-Service (DBaaS) that offers compatibility with the majority of SQL Server features.
* [SQL Server on Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server installed on Windows Server Virtual Machines (VMs) running on Azure Infrastructure-as-a-Service (IaaS).

Learn how each option fits into the Microsoft data platform and get help matching the right option to your business requirements. Whether you prioritize cost savings or minimal administration ahead of everything else, this article can help you decide which approach delivers against the business requirements you care about most.

- [Microsoft’s Data Platform](#platform)
- [A Closer Look at Azure SQL Database and SQL Server on Azure VMs](#close)
- [Business motivations when choosing Azure SQL Database or SQL Server on Azure VMs](#business)
	- [Cost](#cost)
		- [Billing and licensing basics](#billing)
		- [Calculating the total application cost](#appcost)
	- [Administration](#admin)
	- [Service level agreement (SLA)](#sla)
	- [Time to market](#market)
- [Summary](#summary)
- [Acknowledgements](#ack)
- [Additional Resources](#resources)


##<a name="platform"></a>Microsoft's data platform

One of the first things to understand in any discussion of Azure versus on-premises SQL Server databases is that you can use it all. Microsoft’s data platform leverages SQL Server technology and makes it available across physical on-premises machines, private cloud environments, third-party hosted private cloud environments, and public cloud. This enables you to meet unique and diverse business needs through a combination of on-premises and cloud-hosted deployments, while using the same set of server products, development tools, and expertise across these environments.

   ![Cloud SQL Server options: SQL server on IaaS, or SaaS SQL database in the cloud.][1]

As seen in the diagram, each offering can be characterized by the level of administration you have over the infrastructure (on the X axis), and by the degree of cost efficiency achieved by database level consolidation and automation (on the Y axis).

When designing an application, four basic options are available for hosting the SQL Server part of the application:

- SQL Server on nonvirtualized physical machines
- SQL Server in on-premises virtualized machines (private cloud)
- SQL Server in Azure Virtual Machine (public cloud)
- Azure SQL Database (public cloud).

In the following sections, we will learn about SQL Server in the public cloud: Azure SQL Database and SQL Server on Azure VMs. In addition, we will explore common business motivators for determining which option works best for your application.

##<a name="close"></a>A closer look at Azure SQL Database and SQL Server on Azure VMs

**Microsoft Azure SQL Database (Azure SQL Database)** is a relational database-as-a-service (DBaaS) hosted in the Azure cloud that falls into the industry categories of *Software-as-a-Service (SaaS)* and *Platform-as-a-Service (PaaS)*. Azure SQL Database is built on standardized hardware and software that is owned, hosted, and maintained by Microsoft. With Azure SQL Database, you can develop directly on the service using built-in features and functionality. When using Azure SQL Database, you pay-as-you-go with options to scale up or out for greater power.

**SQL Server on Azure Virtual Machines (VMs)** falls into the industry category *Infrastructure-as-a-Service (IaaS)* and allows you to run SQL Server inside a virtual machine in the cloud. Similar to Azure SQL Database, it is built on standardized hardware that is owned, hosted, and maintained by Microsoft. When using SQL Server on a VM, you can either bring your own SQL Server license or use a pre-licensed SQL Server image from the Azure portal.

In general, these two SQL options are optimized for different purposes:

- **Azure SQL Database** is optimized to reduce overall costs to the minimum for provisioning and managing many databases. It reduces ongoing administration costs because you do not have to manage any virtual machines, operating system or database software. This includes upgrades, high availability, and backups. In general, Azure SQL Database can dramatically increase the number of databases managed by a single IT or development resource.
- **SQL Server running on Azure VMs** is optimized for extending existing on-premises SQL Server applications to the cloud in a hybrid scenario or deploying an existing application to Azure in a migration or dev/test scenario. An example of the hybrid scenario is keeping secondary database replicas in Azure via use of [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md). With SQL Server on Azure VMs, you have the full administrative rights over a dedicated SQL Server instance and a cloud-based VM. It is a perfect choice when an organization already has IT resources available to maintain the virtual machines. With SQL Server on VMs, you can build a highly customized system to address your application’s specific performance and availability requirements.

The following table summarizes the main characteristics of Azure SQL Database and SQL Server on Azure VMs:

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle"></th>
   <th align="left" valign="middle">Azure SQL Database</th>
   <th align="left" valign="middle">SQL Server in Azure VM</th>

</tr>
<tr>
   <td valign="middle"><p><b>Best for</b></p></td>
   <td valign="middle">
          <ul>
          <li type=round>New cloud-designed applications that have time constraints in development and marketing.
          <li type=round>Applications that need built-in high availability, disaster recovery, and upgrade mechanisms.
          <li type=round>Teams that do not want to manage the underlying operating system and configuration settings.
         <li type=round>Applications using scale-out patterns.
         <li type=round>Databases of up to 1 TB in size.
         <li type=round>Building Software-as-a-Service (SaaS) applications.
  </ul>
</td>
   <td valign="middle">
      <ul>
      <li type=round>Existing applications that require fast migration to the cloud with minimal changes.
      <li type=round>SQL Server applications that require access to on-premises resources (such as Active Directory) from Azure via a secure tunnel.
      <li type=round>If you need a customized IT environment with full administrative rights.
      <li type=round>Rapid development and test scenarios when you do not want to buy on-premises non-production SQL Server hardware.
      <li type=round>Disaster recovery for on-premises SQL Server applications using <a href="http://msdn.microsoft.com/library/jj919148.aspx">backup to Azure Storage</a> or <a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">AlwaysOn replicas with Azure VMs</a>.
      <li type=round>Large databases that are bigger than 1 TB in size.
      </ul></td>
</tr>
<tr>
   <td valign="middle"><p><b>Resources</b></p></td>
   <td valign="middle">
       <ul>
       <li type=round>You do not want to employ IT resources for support and maintenance of the underlying infrastructure.
       <li type=round>You want to focus on the application layer.
       </ul></td>
   <td valign="middle"><ul><li type=round>You have IT resources for support and maintenance.</ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>Total cost of ownership</b></p></td>
   <td valign="middle"><ul><li type=round>Eliminates hardware costs. Reduces administrative costs.</ul></td>
   <td valign="middle"><ul><li type=round>Eliminates hardware costs. </ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>Business continuity</b></p></td>
   <td valign="middle"><ul><li type=round>In addition to built-in fault tolerance infrastructure capabilities, Azure SQL Database provides features, such as Point in Time Restore, Geo-Restore, and Geo-Replication to increase business continuity. For more information, see <a href="http://msdn.microsoft.com/library/azure/hh852669.aspx">Azure SQL Database Business Continuity</a>.</ul></td>
   <td valign="middle"><ul><li type=round>SQL Server on Azure VMs lets you to set up a high availability and disaster recovery solution for your database’s specific needs. Therefore, you can have a system that is highly optimized for your application. You can test and run failovers by yourself when needed. For more information, see <a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">High Availability and Disaster Recovery for SQL Server on Azure Virtual Machines</a>.</ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>Hybrid cloud</b></p></td>
   <td valign="middle"><ul><li type=round>Your on-premises application can access data in Azure SQL Database.</ul></td>
   <td valign="middle"><ul>
      <li type=round>With SQL Server on Azure VMs, you can have applications that run partly in the cloud and partly on-premises. For example, you can extend your on-premises network and Active Directory Domain to the cloud via <a href="https://azure.microsoft.com/documentation/articles/virtual-networks-overview/">Azure Network Services</a>. In addition, you can store on-premises data files in Azure Storage using the <a href="http://msdn.microsoft.com/library/dn385720.aspx">SQL Server Data Files in Azure feature</a>. For more information, see <a href="http://msdn.microsoft.com/library/dn606154.aspx">Introduction to SQL Server 2014 Hybrid Cloud</a>.
      <li type=round>Supports disaster recovery for on-premises SQL Server applications  using  <a href="http://msdn.microsoft.com/library/jj919148.aspx">backup on Azure Storage</a> or <a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">AlwaysOn replicas in Azure VMs</a>.
      </ul></td>

</tr>
</table>

##<a name="business"></a>Business motivations for choosing Azure SQL Database or SQL Server on Azure VMs

###<a name="cost"></a>Cost

Whether you’re a startup that is strapped for cash, or a team in an established company that operates under tight budget constraints, limited funding is often the primary driver when deciding how to host your databases. In this section, we’ll first learn about the billing and licensing basics in Azure with regards to these two relational database options: Azure SQL Database and SQL Server on Azure VMs. Then, we’ll see how we should calculate the total application cost.

####<a name="billing"></a>Billing and licensing basics

**Azure SQL Database** is sold to customers as a service not with a license whereas SQL Server on Azure VMs requires traditional SQL Server licensing.

Currently, **Azure SQL Database** is available in several service tiers, all of which are billed hourly at a fixed rate based on the service tier and performance level you choose. The Basic, Standard, and Premium service tiers are designed to deliver predictable performance with multiple performance levels to match your application’s peak requirements. You can change between service tiers and performance levels to match your application’s varied throughput needs. For the latest information on the current supported service tiers, see [Azure SQL Database Service Tiers (Editions)](sql-database-service-tiers.md).

With **Azure SQL Database**, the database software is automatically configured, patched, and upgraded by Microsoft which reduces your administration costs. In addition, its [built-in backup](http://msdn.microsoft.com/library/azure/jj650016.aspx) capabilities help you achieve significant cost savings, especially, when you have a large number of databases. When using Azure SQL Database, you are not billed for individual queries running against Azure SQL Database or incoming internet traffic, you are billed for [outgoing internet traffic](http://azure.microsoft.com/pricing/details/data-transfers/). If your database has high transactional volume and needs to support many concurrent users, we recommend that selecting the Premium service tier.

With **SQL Server on Azure VMs**, you utilize traditional SQL Server licensing. You can either use the platform-provided SQL Server image (which includes a license) or bring your SQL Server license. When using the Azure provided images, the operational cost depends on the VM size as well as the edition of SQL Server you choose. Regardless of VM size or SQL Server edition, you pay per-minute licensing cost of SQL Server and  Windows Server, along with the Azure Storage cost for the VM disks. The per-minute billing option allows you to use SQL Server for as long as you need without buying addition SQL Server licenses. If you bring your own SQL Server license to Azure, you are charged for Windows Server and storage costs only. For more information on bring-your-own licensing, see [License Mobility through Software Assurance on Azure](http://azure.microsoft.com/pricing/license-mobility/).

####<a name="appcost"></a>Calculating the total application cost

When you start using a cloud platform, the cost of running your application primarily includes the development and administration costs along with the service costs that the public cloud platform requires.

Here is the detailed cost calculation for your application running in Azure SQL Database and SQL Server on Azure VMs:

**When using Azure SQL Database:**

*Total cost of application = Highly minimized administration costs + software development costs + Azure SQL Database service costs*

**When using SQL Server on Azure VMs:**

*Total cost of application = Minimized software development/modification costs + administration costs +  SQL Server and Windows Server licensing costs + Azure Storage costs*

> [AZURE.IMPORTANT] Currently, Azure SQL Database does not support all the features of SQL Server. For a detailed comparison information, see [Azure SQL Database Guidelines and Limitations](http://msdn.microsoft.com/library/azure/ff394102.aspx). Be aware of this when considering moving an existing SQL Server database to Azure SQL Database. When you migrate an existing on-premises SQL Server application to Azure SQL Database, we recommend that you update the application to take all advantages of the capabilities of the new environment. For example, start using [Azure Web App Service](https://azure.microsoft.com/en-us/services/app-service/web/) or [Azure Cloud Services](http://azure.microsoft.com/services/cloud-services/) to host your application layer to increase cost benefits. In addition, validate your application against different Azure SQL Database service tiers and check which one best meets to your application’s needs. This process helps you achieve better performance results and minimize costs. For more information, see [Azure SQL Database Service Tiers and Performance Levels](sql-database-service-tiers.md).

For a detailed cost estimate, use the [Azure Pricing Calculator](http://azure.microsoft.com/pricing/calculator/).

For more information on pricing, see the following resources:

- [Azure SQL Database Pricing Details](http://azure.microsoft.com/pricing/details/sql-database/)
- [Virtual Machine Pricing Details](http://azure.microsoft.com/pricing/details/virtual-machines/)
- [SQL Server on Azure VMs - Pricing Details](http://azure.microsoft.com/pricing/details/virtual-machines/#sql-server)
- [Windows Server on Azure VMs - Pricing Details](http://azure.microsoft.com/pricing/details/virtual-machines/#windows)

###<a name="admin"></a>Administration

For many businesses, the decision to transition to a cloud service is as much about offloading complexity of administration as it is cost. With **Azure SQL Database**, Microsoft administers the underlying hardware; automatically replicates all data to provide high availability; configures and upgrades the database software; manages load balancing; and does transparent failover if there is a server failure. You can continue to administer your Azure SQL Database instances, but you no longer need to manage the database engine, server operating sytem or hardware.  Examples of items you can continue to administer include databases and logins, index and query tuning, and auditing and securit. For more information, see [Azure SQL Database Guidelines and Limitations](http://msdn.microsoft.com/library/ff394102.aspx).

On the other hand, you might have in-house expertise and a desire to keep control over database location down to the location of disk. With **SQL Server running on Azure VMs**, you have full control over the operating system and SQL Server instance configuration. With a VM, it’s up to you to decide when to update/upgrade the operating system and database software and when to install any additional software such as anti-virus and backup tools. In addition, you can control the size of the VM, the number of disks, and their storage configurations.  For example, Azure allows you to change the size of a VM as needed. For information, see [Virtual Machine and Cloud Service Sizes for Azure](../virtual-machines/virtual-machines-size-specs.md).

###<a name="sla"></a>Service Level Agreement (SLA)

For many IT departments, meeting up-time obligations of a Service Level Agreement (SLA) is a top priority. In this section, we look at what SLA applies to each database hosting option.

For **Azure SQL Database** Basic, Standard, and Premium service tiers Microsoft provides an availability SLA of 99.99%.  This SLA covers the ability to connect to the database - it is not a performance SLA. For the latest information on Azure Database SLAs, see [Service Level Agreement](https://azure.microsoft.com/en-us/support/legal/sla/sql-database/). For the latest information on Azure SQL Database Service Tiers (Editions) and the supported business continuity plans, see [Azure SQL Database Service Tiers](sql-database-service-tiers.md).

For **SQL Server running on Azure VMs**, Microsoft provides an availability SLA of 99.95% that covers just the Virtual Machine. This SLA does not cover the processes (such as SQL Server) running on the VM. The [VM SLA](https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/) requires that you host at least two VM instances in an availability set. With such configuration, Microsoft guarantees that at least one of the VMs will be available 99.95% of the time.  For database high availability (HA) within VMs, you should configure one of the supported high availability options in SQL Server, such as [AlwaysOn Availability Groups](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

###<a name="market"></a>Time to market

**Azure SQL Database** is the right solution for cloud-designed applications when developer productivity and fast time-to-market are critical. With programmatic DBA-like functionality, it is perfect for cloud architects and developers as it lowers the need for managing the underlying operating system and database. For example, you can use the [REST API](http://msdn.microsoft.com/library/azure/dn505719.aspx) and [PowerShell Cmdlets](http://msdn.microsoft.com/library/azure/dn546726.aspx) to automate and manage administrative operations for thousands of databases. Features such as [Elastic Database Pools](sql-database-elastic-pool.md) allow you to focus on the application layer and deliver your solution to the market faster.

**SQL Server running on Azure VMs** is perfect if your existing or new applications require access and control to all features of a SQL Server instance. It is also a good fit when you want to migrate existing on-premises applications and databases to Azure as-is. Since you do not need to change the presentation, application, and data layers, you save time and budget on rearchitecting your existing solution. Instead, you can focus on migrating all your solutions to Azure and in doing some performance optimizations that may be required by the Azure platform. For more information, see [Performance Best Practices for SQL Server on Azure Virtual Machines](../virtual-machines/virtual-machines-sql-server-performance-best-practices.md).

##<a name="summary"></a>Summary

In this article, we explored: Azure SQL Database and SQL Server on Azure Virtual Machines (VMs). In addition, we discussed common business motivators that might affect decision making on which of the two to choose.

The following is a summary of suggestions for you to consider:

Choose **Azure SQL Database**, if:

- You are building new, cloud-based applications; or you want to migrate your existing SQL Server database to Azure and your database is not using one of the unsupported features in Azure SQL Database. For more information, see [Azure SQL Database Transact-SQL Reference](http://msdn.microsoft.com/library/azure/ee336281.aspx). This approach provides the benefits of a fully managed cloud service and helps lower time-to-market.

- You want to have Microsoft perform common management operations on your databases and require stronger availability SLAs for databases.

    [Create your first Azure SQL database](sql-database-get-started.md)


Choose **SQL Server on Azure VMs**, if:

- You have existing on-premises applications and wish to stop maintaining your own hardware; or you are considering hybrid solutions. This approach lets you access high database capacity sooner while facilitating connection to your on-premises applications via a secure tunnel.

- You have existing IT resources, need full administrative rights over SQL Server, and require the full compatibility with on-premises SQL Server. This approach lets you minimize costs for development or modifications of existing applications with the flexibility to run most applications. In addition, it provides full control of the VM, operating system, and database configuration.

    [Provision a SQL Server virtual machine in Azure](virtual-machines-provision-sql-server.md)

> [AZURE.NOTE] Do you want to try out SQL Server 2016 CTP2? Sign up for Microsoft Azure, and then go [here](http://aka.ms/sql2016vm "here") to create a Virtual Machine with SQL Server 2016 CTP2 already installed.

##<a name="ack"></a>Acknowledgements

This article from the Microsoft Cloud and Enterprise Content Services group was produced with the help of many people within the Microsoft community.

**Author:** Selcin Turkarslan

**Technical Contributors:** Conor Cunningham

**Technical Reviewers:** Joanne Marone (Hodgins), Karthika Raman, Lindsey Allen, Lori Clark, Luis Carlos Vargas Herring, Nosheen Syed Wajahatulla Hussain, Pravin Mittal, Shawn Bice, Silvano Coriani, Tony Petrossian, Tracy Daugherty.

**Editorial Reviewers:** Heidi Steen, Maggie Sparkman.

Thank you all for bringing this article to life!

##<a name="resources"></a>Additional resources

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Resource</th>
   <th align="left" valign="middle">Description</th>
</tr>
<tr>
   <td valign="middle"><p><a href="https://azure.microsoft.com/en-us/documentation/articles/sql-database-technical-overview/">What is Azure SQL Database?</a></p>
<p><a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/">Overview of SQL Server on Azure Virtual Machines</a></p>

<p><a href="http://azure.microsoft.com/services/sql-database/">Azure SQL Database service offering</a></p></td>
   <td valign="middle">Links to the library documentation.</td>   
</tr>
<tr>
   <td valign="middle"><p><a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-application-patterns-and-development-strategies/">Application Patterns and Development Strategies for SQL Server on Azure Virtual Machines</p></td>
   <td valign="middle">This article discusses the most common application patterns that apply to SQL Server on Azure VMs and also hybrid scenarios including Azure SQL Database.</td>   
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/hh680934(v=PandP.50).aspx">Microsoft Enterprise Library Transient Fault Handling Application Block</p></td>
   <td valign="middle">This library lets developers make their applications running on Azure SQL Database more resilient by adding robust transient fault handling logic. Transient faults are errors that occur because of some temporary condition such as network connectivity issues or service unavailability. Since Azure SQL Database is a multitenant service, it is important to handle such errors to minimize any application downtime. </td>   
</tr>
<tr>
<td valign="middle"><p><a href="http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling">Transient Fault Handling (Building Real-World Cloud Apps with Azure)</p></td>
<td valign="middle">
This site walks you through the patterns you can use to manage transient failures of Azure SQL Database (and other Azure services). The provided code samples will help developers who are new to this space and shows you how you can use the Transient Fault Handling Application Block (amongst other technologies) in your code.
</td>
</tr>
</table>

<!--Image references-->
[1]: ./media/data-management-azure-sql-database-and-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png
