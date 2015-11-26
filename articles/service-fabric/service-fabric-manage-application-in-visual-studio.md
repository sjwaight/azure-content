<properties
   pageTitle="Manage your applications in Visual Studio | Microsoft Azure"
   description="Use Visual Studio to create, develop, package, deploy, and debug your Service Fabric applications and services."
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
   ms.date="11/17/2015"
   ms.author="jesseb"/>

# Use Visual Studio to simplify writing and managing your Service Fabric applications

You can manage your Service Fabric applications and services through Visual Studio. Once you've [setup your development environment](service-fabric-setup-your-development-environment.md), you can use Visual Studio to create Service Fabric applications, add services, or package, register, and deploy applications in your local development cluster.

To manage your application, in the Solution Explorer right-click on your application project.

![Manage your Service Fabric application by right-clicking on the Application project][manageservicefabric]

## Deploying your Service Fabric application

Deploying an application combines the following steps into one simple operation.

1. Creating the application package
2. Uploading the application package to the image store
3. Registering the application type
4. Removing any running application instances
5. Creating a new application instance

In Visual Studio, pressing **F5** will also deploy your application and attach the debugger to all application instances. You can use **Ctrl+F5** to deploy an application without debugging, or publish to a local or remote cluster using the publish profile. Please refer to [Publish an application to a remote cluster using Visual Studio](service-fabric-publish-app-remote-cluster.md)

### Preserve data between test runs

Often, you test services locally by adding test data input, modifying a few code blocks, and then debugging locally again. The Visual Studio Service Fabric Tools provide a handy property called **Preserve Data on Start** to keep the data you entered in the previous session and let you use it again.

### To enable the Preserve Data on Start property

1. On the application project's shortcut menu, choose **Properties** (or choose the **F4** key).
1. In the **Properties** window, set the **Preserve Data on Start** property to **Yes**.

	![Set the Preserve Data on Start property][preservedata]

When you run your application again, the deployment script now treats the deployment as an upgrade using unmonitored auto mode to quickly upgrade the application to a newer version with a date string appended. The upgrade process preserves any data you entered in a previous debug session.

![Example of new application version with date appended][preservedate]

Data is preserved leveraging the upgrade capability from the Service Fabric platform. For more information about upgrade an application please refer to [Service Fabric application upgrade](service-fabric-application-upgrade.md)

## Adding a service to your Service Fabric application

You can add new Fabric Services to your application to extend its functionality.  To ensure the service is included in your application package, add the service through the **New Fabric Service...** menu item.

![Add a new Fabric Service to your application][newservice]

Select a Service Fabric project type to add to your application, and specify a name for the service.  See [choosing a framework for your service](service-fabric-choose-framework.md) to help decide which service type to use.

![Select a Fabric Service project type to add to your application][addserviceproject]

The new service will be added to your solution and existing application package. The service references and a default service instance will be added to the application manifest. The service will be created and started the next time you deploy the application.

![The new service will be added to your application manifest][newserviceapplicationmanifest]

## Packaging your Service Fabric application

An application package needs to be created in order to deploy the application and its services to a cluster.  The package organizes the application manifest, service manifest(s), and other necessary files in a specific layout.  Visual Studio sets up and manages the package in the application project's folder, in the 'pkg' directory.  Clicking **Package**  from the **Application** context menu creates or updates the application package.  You may want to do this if you deploy the application using custom PowerShell scripts.

## Removing an application

You can unprovision an application type from your local cluster using Service Fabric Explorer.  The cluster explorer is accessible from the cluster's HTTP Gateway endpoint (typically 19080 or 19007), e.g. http://localhost:19080/Explorer.  This will revert the deployment steps described above:

1. Remove any running application instances
2. Unregister the application type

![Remove an application](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Next steps

- [Service Fabric application model](service-fabric-application-model.md)
- [Service Fabric application deployment](service-fabric-deploy-remove-applications.md)
- [Managing application parameters for multiple environments](service-fabric-manage-multiple-environment-app-configuration.md)
- [Debugging your Service Fabric application](service-fabric-debugging-your-application.md)
- [Visualizing your cluster using Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]:./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[preservedate]:./media/service-fabric-manage-application-in-visual-studio/preservedate.png
