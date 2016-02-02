<properties
   pageTitle="Visualizing your cluster using Service Fabric Explorer | Microsoft Azure"
   description="Service Fabric Explorer is a useful GUI tool for inspecting and managing cloud applications and nodes in a Microsoft Azure Service Fabric cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/30/2015"
   ms.author="jesseb"/>

# Visualizing your cluster using Service Fabric Explorer

Service Fabric Explorer is a web-based tool for inspecting and managing applications and nodes in a Service Fabric cluster. Service Fabric Explorer is hosted directly within the cluster so it is always available, regardless of where your cluster is running.

## Connecting to Service Fabric Explorer

If you have followed the instructions to [prepare your development environment](service-fabric-get-started.md), you can launch Service Fabric Explorer on your local cluster by navigating to http://localhost:19080/Explorer.

>[AZURE.NOTE] If you are using Internet Explorer(IE) with Service Fabric Explorer to manage a remote cluster, you need to configure some IE settings. Go to **Tools -> Compatibility View Settings** and uncheck **Display intranet sites in Compatibility View** to ensure all information loads correctly.

## Understanding Service Fabric Explorer layout

You can navigate Service Fabric Explorer using the tree on the left. At the root of the tree, the cluster dashboard provides an overview of your cluster, including a summary of application and node health.

![Service Fabric Explorer cluster dashboard][sfx-cluster-dashboard]

### The cluster map

Nodes in a Service Fabric cluster are placed across a 2-dimensional grid of fault domains and upgrade domains to ensure that your applications remains available in the presence of hardware failures and application upgrades. You can view how the current cluster is laid out using the Cluster Map.

![Service Fabric Explorer cluster map][sfx-cluster-map]

### Viewing applications and services

The cluster contains two sub-trees: one for applications and another for nodes.

The applications view allows you to navigate through Service Fabric's logical hierarchy: applications, services, partitions, and replicas.

In the example below, the application **MyApp** is made up of two services, **MyStatefulService** and **WebService**. Since **MyStatefulService** is stateful, it includes a partition with one primary and two secondary replicas. By contrast, the WebSvcService is stateless and contains a single instance.

![Service Fabric Explorer application view][sfx-application-tree]

At each level of the tree, the main pane shows pertinent information about the item. For instance, you can see the health status and version for a particular service.

![Service Fabric Explorer essentials pane][sfx-service-essentials]

### Viewing the cluster's nodes

The Nodes view shows the physical layout of the cluster. For a given node, you can inspect which applications have code deployed on that node and more specicially, which replicas are currently running there.

## Taking actions using Service Fabric Explorer

Service Fabric Explorer offers a quick way to invoke actions on nodes, applications, and services within your cluster.

For instance, to delete an application instance, simply choose the application from the tree on the left, then choose Actions > Delete Application.

![Deleting an application in Service Fabric Explorer][sfx-delete-application]

Since many actions are destructive, you will be asked to confirm your intent before the action is completed.

>[AZURE.NOTE] Every action that can be performed using Service Fabric Explorer can also be performed using PowerShell or a REST API, enabling automation.



## Connecting to a remote Service Fabric cluster

Since Service Fabric Explorer is web-based and runs within the cluster, it is accessible from any browser, as long as you know the cluster's endpoint and have sufficient permissions to access it.

### Discovering the Service Fabric Explorer endpoint for a remote Cluster

In order to reach Service Fabric Explorer for a given cluster, simply point your browser to:

http://&lt;your-cluster-endpoint&gt;:19080/Explorer

The full URL is also available in the cluster essentials pane of the Azure portal.

### Connecting to a secure cluster

You can control access to your Service Fabric cluster by requiring clients to present a certificate in order to connect to it.

If you attempt to connect to Service Fabric Explorer on a secure cluster, your browser will ask to present a certificate in order to gain access.

## Next steps

- [Testability overview](service-fabric-testability-overview.md).
- [Managing your Service Fabric applications in Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Service Fabric application deployment using PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
