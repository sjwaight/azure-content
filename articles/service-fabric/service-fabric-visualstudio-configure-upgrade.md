<properties
   pageTitle="Configure the upgrade of a Service Fabric application | Microsoft Azure"
   description="Learn how to configure the settings for upgrading a Service Fabric application by using Microsoft Visual Studio."
   services="service-fabric"
   documentationCenter="na"
   authors="cawaMS"
   manager="paulyuk"
   editor="tglee" />
<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="10/08/2015"
   ms.author="cawaMS" />

# Configure the upgrade of a Service Fabric application in Visual Studio

Visual Studio Service Fabric Tools provide upgrade support for publishing to local or remote clusters. There are two advantages for upgrading your application to a newer version instead of replacing the application during testing and debugging: firstly application data won't be lost during the upgrade, and secondly high availability is achieved as there won't be any service interruption during upgrade if there are enough service instances spread across upgrade domains. Tests can be run against an application while it's being upgraded. 

## Parameters needed to upgrade

There are two types of deployment you can choose: regular or upgrade. A regular deployment erases any previous deployment information and data on the cluster, while an upgrade deployment preserves it. When you upgrade a Service Fabric application in Visual Studio, you need to provide application upgrade parameters and health check policies. Application upgrade parameters help control the upgrade while health check policies determine whether the upgrade was successful or not. See [Service Fabric Application Upgrade: Upgrade Parameters](service-fabric-application-upgrade-parameters.md) for more details.

There are three upgrade modes: *Monitored*, *UnmonitoredAuto*, and *Manual*.

  - A *Monitored* upgrade automates the upgrade and application health check.

  - An* UnmonitoredAuto* upgrade automates the upgrade, but skips the application health check.

  - When you do a *Manual* upgrade, you need to manually upgrade each upgrade domain.

Each upgrade mode requires different sets of parameters. See [Application Upgrade Parameters](service-fabric-application-upgrade-parameters.md) to learn more about the available upgrade options.

## Upgrade a Service Fabric application in Visual Studio

If you’re using the Visual Studio Service Fabric Tools to upgrade a Service Fabric application, you can specify a publish process to be an upgrade rather than a regular deployment by checking the **Upgrade the application** checkbox.

### To configure the upgrade parameters

1. Click the **Settings** button next to the checkbox. The **Edit Upgrade Parameters** dialog appears. The **Edit Upgrade Parameters** dialog supports the *Monitored*, *UnmonitoredAuto* and *UnmonitoredManual* upgrade modes.

2. Select the upgrade mode you want to use and then fill out the parameter grid.

    Each parameter has default values. The optional parameter *DefaultServiceTypeHealthPolicy* parameter takes a hash table input. Here’s an example of the hash table input format for *DefaultServiceTypeHealthPolicy*:

	```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
	```
	
    *ServiceTypeHealthPolicyMap* is another optional parameter that takes a hash table input in the following format:

	```    
	@ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
	```


    Here's a real-life example:

    ```
	@{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
	```

3. If you select *UnmonitoredManual* upgrade mode, you will have to manually start a PowerShell console to continue and finish the upgrade process. Please refer to [Service Fabric Application Upgrade: Advanced Topics](service-fabric-application-upgrade-advanced.md) to learn how manual upgrade works

## Upgrade an application using PowerShell

You can use PowerShell cmdlets to upgrade a Service Fabric application. See [Service Fabric Application Upgrade Tutorial](service-fabric-application-upgrade-tutorial.md) and [Start-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/mt125975.aspx) for detailed information.

## Specify a health check policy in the Application Manifest file

Every service in a Service Fabric application can have its own health policy parameters that override the default values. You can provide these parameter values in the Application Manifest file.

The following example shows how to apply a unique health check policy for each service in the application manifest.

```
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## Next steps
For more information about deploying an application, see [Deploy an existing application in Azure Service Fabric](service-fabric-deploy-existing-app.md).

