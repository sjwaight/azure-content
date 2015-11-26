<properties
	pageTitle="What is Azure Backup? | Microsoft Azure"
	description="With Azure Backup and recovery services you can backup and restore data and applications from Windows servers, Windows client machines, SCDPM servers or Azure virtual machines."
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor="tysonn"
	keywords="backup and restore; recovery services"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/17/2015"
	ms.author="aashishr"; "trinadhk"; "jimpark"/>

# What is Azure Backup?
Azure Backup is a multi-tenanted Azure service that enables you to back up and restore your data on-premises or in Azure. It replaces your existing on-premises or offsite backup solution with a reliable, secure and cost competitive cloud backup solution. It also provides the flexibility of protecting your assets running in the cloud. Azure Backup is built on top of a world class infrastructure that is scalable, durable and highly available. Using this solution, you can back up data and applications from their System Center Data Protection Manager (SCDPM) servers, Windows servers, Windows client machines or Azure IaaS virtual machines. Azure Backup and SCDPM are the fundamental technologies which make up Microsoft's cloud-integrated backup solution.

> [AZURE.VIDEO what-is-azure-backup]

## Cloud Design point
Traditional backup solutions have evolved to treat the cloud as an endpoint similar to disk or tape. While this approach is simple, easy to deploy and provides a consistent experience, it has limited uses and does not take full advantage of the underlying platform. This translates to an inefficient, expensive solution for end customers. By treating Azure as "just as a storage endpoint", backup solutions are unable to tap into the richness and power of the public cloud platform. Azure Backup, on the other hand, delivers a true service which uses the cloud to deliver a powerful & affordable backup solution. It integrates with your on-premises backup solution (SCDPM) to provide an end-to-end hybrid solution.

The advantages to this approach are:

- Efficient cloud storage architecture that provides low-cost, resilient data storage
- Nonintrusive, auto scaling of the service with high availability guarantees
- Consistent way to back up on premise, hybrid and IaaS deployments

The key features of this solution are:

1. **Reliable service**: By adopting Azure Backup, you get a backup service which is highly available. The service is multi-tenanted and you do not have to worry about managing the underlying compute or storage.

2. **Reliable storage**: Azure Backup is built on top of reliable cloud storage which is backed by high SLAs. You do not have to worry about capital or operation expenses in maintaining the storage. Furthermore you have the choice of backing up to LRS (Locally Redundant Storage) or GRS (Geo Replication storage) storage. While LRS enables 3 copies of the data in the same geo which protects against local hardware failures; GRS provides 3 additional copies (a total of 6 copies) in a paired data center. This ensures that your backup data is highly available. Even if there is an Azure-site-level disaster, the backup data is safe with us.

3. **Secure**: Azure backup data is encrypted at source, during transmission and stored encrypted in Azure.  The encryption key is stored at source and is never transmitted or stored in Azure. The encryption key is required to restore any of the data and only the customer has full access to the data in the service.

4. **Offsite protection**: Rather than paying for offsite tape backup solutions, customers can backup to Azure which provides a compelling solution with tape-like-semantics at a very low cost.

5. **Simplicity**: Azure Backup provides a familiar interface that can scale to protect a deployment of any size.  As the service evolves, features like central management will allow you to manage your backup infrastructure from a single location.

6. **Cost effective**:  Azure Backup pricing includes a per-instance backup management fee and cost of storage (block blob price) consumed on Azure.  Unlike other cloud based backup offering, Azure Backup does not charge its customers for any restore operation. Furthermore, customers are not charged for any egress (outbound) data transfer cost during a restore operation.

7. **Backup in Cloud**: Azure Backup provides VSS-based application-consistent backup of Azure IaaS virtual machines running without the need to shut down the virtual machine. It can also backup Linux virtual machines in Azure with filesystem consistency.


## Deployment scenarios
| Component | Can be deployed in Azure? | Can be deployed on-premises? | Target storage supported|
| --- | --- | --- | --- |
| Azure Backup agent | **Yes** <br><br>The Azure Backup agent can be deployed on any Windows Server VM running in Azure. | **Yes** <br><br>The Azure Backup agent can be deployed on any Windows Server VM or physical machine. | Azure Backup vault |
| System Center Data Protection Manager (SCDPM) | **Yes** <br><br>Learn more about [protecting workloads in Azure using SCDPM](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx). | **Yes** <br><br>Learn more about [protecting workloads and VMs in your datacenter](https://technet.microsoft.com/en-us/library/hh758173.aspx). | Locally attached disk,<br>Azure Backup vault,<br>Tape (on-premises only) |
| Azure Backup (VM extension) | **Yes** <br><br>Specialized for [backup of Azure IaaS virtual machines](backup-azure-vms-introduction.md). | **No** <br><br>Use SCDPM to backup virtual machines in your datacenter. | Azure Backup vault |


## Applications and workloads

| Workload | Source machine | Azure Backup solution |
| --- | --- |---|
| Files & folders | Windows Server | [Azure Backup agent](backup-configure-vault.md),<br> [System Center DPM](backup-azure-dpm-introduction.md) |
| Files & folders | Windows client | [Azure Backup agent](backup-configure-vault.md),<br> [System Center DPM](backup-azure-dpm-introduction.md) |
| Hyper-V Virtual machine (Windows) | Windows Server | System Center DPM |
| Hyper-V Virtual machine (Linux) | Windows Server | System Center DPM |
| Microsoft SQL Server | Windows Server | [System Center DPM](backup-azure-backup-sql.md) |
| Microsoft SharePoint | Windows Server | [System Center DPM](backup-azure-backup-sharepoint.md) |
| Microsoft Exchange |  Windows Server | System Center DPM |
| Azure IaaS VMs (Windows)|  - | [Azure Backup (VM extension)](backup-azure-vms-introduction.md) |
| Azure IaaS VMs (Linux) | - | [Azure Backup (VM extension)](backup-azure-vms-introduction.md) |


## Next steps
- [Try Azure Backup](backup-try-azure-backup-in-10-mins.md)
- Frequently asked question on the Azure Backup service is listed [here](backup-azure-backup-faq.md).
- Visit the [Azure Backup Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933).
