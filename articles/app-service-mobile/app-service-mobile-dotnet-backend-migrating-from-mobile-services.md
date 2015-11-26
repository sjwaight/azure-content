﻿<properties 
	pageTitle="Migrate from Mobile Services to an App Service Mobile App" 
	description="Learn how to easily migrate your Mobile Services application to an App Service Mobile App" 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/04/2015" 
	ms.author="mahender"/>

# Migrate your existing Azure Mobile Service to App Service 

This topic shows you how to migrate an existing application from Azure Mobile Services to a new App Service Mobile App. All existing Mobile Services apps can be easily migrated to a new App Service Mobile app. During a migration, your existing Mobile Services application can continue to operate. 

>[AZURE.NOTE] Currently, .NET backend Mobile Services can be migrated to App Service using a manual migration. A turnkey migrate experience for both Node.js and .NET is coming soon. However, if you perform a manual migration, you *cannot* preserve your existing **service.azure-mobile.net** URL.

When an app is migrated to Azure App Service, it has access to all App Service features and are billed according to [App Service pricing], not Mobile Services pricing. 

### <a name="why-host"></a>Why host in App Service?

App Service provides a more feature-rich hosting environment for your application. By hosting in App Service, your service gets access to staging slots, custom domains, Traffic Manager support, and more. While you can [migrate a mobile service to a mobile app on App Service], some customers may wish to take advantage of these features immediately, without performing the SDK update just yet.  

The main limitation to hosting in App Service is that the Mobile Services scheduled jobs are not integrated, and either the Azure Scheduler must be set up to hit custom APIs, or the Web Jobs features must be enabled.

For more on the benefits of App Service, see the [Mobile Services vs. App Service] topic.

##Migrate vs. upgrade

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

  - For Node.js-based server projects, the new [Mobile Apps Node.js SDK](https://github.com/Azure/azure-mobile-apps-node) provides a number of new features. For instance, you can now do local development and debugging, use any Node.js version above 0.10, and customize with any Express.js middleware.

  - For .NET-based server projects, the new [Mobile Apps SDK NuGet packages](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) has more flexibility on NuGet dependencies, supports the new App Service authentication features, and composes with any ASP.NET project, including MVC. To learn more about upgrading, see [Upgrade your existing .NET Mobile Service to App Service](app-service-mobile-net-upgrading-from-mobile-services.md).


##<a name="understand"></a>Understanding App Service Mobile 

App Service Mobile is a new way to build mobile applications using Microsoft Azure. You can learn more about Mobile Apps in the [What are Mobile Apps?] topic.

In a migration to Mobile Apps, all existing app functionality (and code) can be preserved. Moreover, new features are available to the application. In the Mobile Apps model, your code actually runs on a Web App (the new version of Azure Web Sites). You have full control over the web app and how it operates. In addition, Web Apps features which were previously unavailable to Mobile Services customers, such as Traffic Manager and Development Slots, can now be used. 


##Manual migration for Mobile Services .NET backend

This section shows you how to host a .NET Mobile Services project within Azure App Service. An app hosted in this way can use all of the *Mobile Services* .NET runtime tutorials, although it is necessary to substitute any URL using the azure-mobile.net domain with the domain of the App Service instance.

A Mobile Services project can also be hosted in an [App Service Environment]. All of the content in this topic still applies, but the site will have the form contoso.contosoase.p.azurewebsites.net instead of contoso.azurewebsites.net.

>[AZURE.NOTE] If you perform a manual migration, you *cannot* preserve your existing **service.azure-mobile.net** URL. If you have mobile clients that are connecting to this URL, you should either use the automatic migration option, or keep your mobile service running until all mobile clients have upgraded to the new URL. 


### <a name="app-settings"></a>Application Settings
Mobile Services requires several application settings to be available in the environment, which are described in this section.

In order to set the application settings on your web app, first sign into the [Preview Azure Management Portal]. Navigate to the web, mobile, or API app you wish to use and select "Settings," and then "Application Settings." Scroll down to the section labeled "App settings." Here you can set the required key-value pairs.
 
**MS_MobileServiceName** should be set to the name of your app. For example, if you will be running in an app with the URL contoso.azurewebsites.net, then "contoso" would be the correct value.
 
**MS_MobileServiceDomainSuffix** should be set to the name of your web app. For example, if you will be running in an app with the URL contoso.azurewebsites.net, then "azurewebsites.net" would be the correct value.
 
**MS_ApplicationKey** can be set to any value, but it must be the same value used by the client SDK. A GUID is recommended.
 
**MS_MasterKey** can be set to any value, but it must be the same value used by any admin APIs/clients. A GUID is recommended.
 
If moving over an existing Mobile Services Application, both the master key and the application key can be obtained from the "Configure" tab of the Mobile Services section of the [Azure Management Portal]. Select the "Manage Keys" action at the bottom and copy out the keys.


### <a name="client-sdk"></a>How to: Modify the client SDK

In the client app project, modify the constructor of the Mobile Services client object to accept the new app URL (e.g., `https://contoso.azurewebsites.net`) and the application key configured earlier. The client SDK version should be a **Mobile Services** version and should **not** be upgraded. For iOS and Android clients, use 2.x versions, and for Windows/Xamarin, use 1.3.2. Javascript clients should be using 1.2.7.

### <a name="data"></a>How to: Enable data features

In order to work with the default Entity Framework classes in Mobile Services, two further App Settings are needed.
 
Connection strings are stored in the "Connection strings" section of the Application Settings blade, just below the **App settings** section. The connection string for your database should be set under the **MS_TableConnectionString** key. For moving over an existing Mobile Services application, navigate to the "Connection Strings" section of the Mobile Services portal's Configure tab. Click "Show Connection Strings" and copy out the value.
 
By default, the schema to be used is **MS_MobileServiceName**, but this can be overwritten with the **MS_TableSchema** setting. Back under **App settings** set **MS_TableSchema** to be the name of the schema to be used. If you are moving over an existing Mobile Services application, a schema was already created using Entity Framework - this is the name of the Mobile Service, not the App Service instance that will be hosting the code now.

### <a name="push"></a>How to: Enable push features

For push to work, the web app additionally needs to know information about the Notification hub.
 
Under "Connection strings," set **MS_NotificationHubConnectionString** with the DefaultFullSharedAccessSignature connection string for the Notification Hub. For moving over an existing Mobile Services application, navigate to the "Connection Strings" section of the Mobile Services portal's Configure tab. Click "Show Connection Strings" and copy out the value.

The **MS_NotificationHubName** app setting should be set to the name of the hub. When moving an existing Mobile Services app, you can get this value from the push tab in the Mobile Services portal. The other fields on this tab are linked to the hub itself and do not need to be copied anywhere.
 
### <a name="auth"></a>How to: Enable auth features

The identity features also have app setting requirements for the individual providers. If moving from an existing Mobile Services app, each of the fields in the Identity tab of the Mobile Services portal has a corresponding app setting.
 
Microsoft Account

* **MS_MicrosoftClientID**

* **MS_MicrosoftClientSecret**

* **MS_MicrosoftPackageSID**
 
Facebook

* **MS_FacebookAppID**

* **MS_FacebookAppSecret**
 
Twitter

* **MS_TwitterConsumerKey**

* **MS_TwitterConsumerSecret**
 
Google

* **MS_GoogleClientID**

* **MS_GoogleClientSecret**
 
AAD

* **MS_AadClientID**

* **MS_AadTenants** - Note: **MS_AadTenants** is stored as a comma-separated list of tenant domains (the "Allowed Tenants" fields in the Mobile Services portal).

### <a name="publish"></a>How to: Publish the Mobile Services project

1. In the Preview portal, navigate to your app and select "Get publish profile" from the command bar. Save the downloaded profile to the local machine.
2. In Visual Studio, right click on the Mobile Services server project and select "Publish." On the profile tab, choose "Import" and browse to the downloaded profile.
3. Click publish to deploy the code to App Service.

Logs are handled via the standard App Service logging features. To learn more about logging, see [Enable diagnostics logging in Azure App Service].



<!-- URLs. -->

[Preview Azure Management Portal]: https://portal.azure.com/
[Azure Management Portal]: https://manage.windowsazure.com/
[What are Mobile Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[Send cross-platform push notifications]: app-service-mobile-xamarin-ios-push-notifications-to-user.md 
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md


[Enable diagnostics logging in Azure App Service]: web-sites-enable-diagnostic-log.md
[App Service pricing]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[App Service Environment]: app-service-app-service-environment-intro.md
[Mobile Services vs. App Service]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[migrate a mobile service to a mobile app on App Service]: app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md
