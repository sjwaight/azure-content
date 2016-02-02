﻿<properties 
	pageTitle="Upgrade from Mobile Services to Azure App Service" 
	description="Learn how to easily upgrade your Mobile Services application to an App Service Mobile App" 
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
	ms.date="11/03/2015" 
	ms.author="mahender"/>

# Upgrade your existing .NET Azure Mobile Service to App Service

App Service Mobile is a new way to build mobile applications using Microsoft Azure. To learn more, see [What are Mobile Apps?].

This topic describes how to upgrade an existing .NET backend application from Azure Mobile Services to a new App Service Mobile App. When performing this upgrade, your existing Mobile Services application can continue to operate. 

When a mobile backend is upgraded to Azure App Service, it has access to all App Service features and are billed according to [App Service pricing], not Mobile Services pricing.

##Migrate vs. upgrade

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

###Improvements in Mobile Apps .NET server SDK

Upgrading to the new [Mobile Apps SDK NuGet packages](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) provides the following benefits:

- More flexibility on NuGet dependencies. The hosting environment no longer provides its own versions of NuGet packages, so you can use alternative compatible versions. However, if there are new critical bugfixes or security updates to the Mobile Server SDK or dependencies, you must update your service manually.

- Support for new App Service authentication features, which allow you to use a common authentication configuration across your web and mobile apps. Authentication features are also now in their own OWIN middleware component, which means that the same authentication setup can be used for mobile and web routes.

- Support for other ASP.NET project types and routes. You can now host MVC and Web API controllers in the same project as your mobile backend project.

- More flexibility in the mobile SDK. You can explicitly control which features and routes are set up, such as authentication, table APIs, and the push registration endpoint. To learn more, see [How to use the .NET server SDK for Azure Mobile Apps](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup).

- More options for DI frameworks. The Mobile Services .NET server SDK had a dependency on autofac, but the Mobile Apps server SDK allows you to use other frameworks, such as [Unity](https://unity.codeplex.com/).

>[AZURE.NOTE] The Mobile Services client SDKs are **not** compatible with the new Mobile Apps server SDK. If you have mobile client apps that are connecting to your existing mobile service, you should keep the service running until all mobile clients have been upgraded to the  Mobile Apps **client SDK**.
> 
> The existing mobile service can be automatically *migrated* to App Service without any changes to existing client apps. To learn more about migration, see [Migrate your existing Mobile Service to App Service].


##<a name="overview"></a>Basic upgrade overview
The simplest way to upgrade is to create a new instance of an App Service Mobile App. In many cases, upgrading will be as simple as switching to the new Mobile Apps server SDK and republishing your code onto a new Mobile App. There are, however some scenarios which will require some additional configuration, such as advanced authentication scenarios and working with scheduled jobs. Each of these is covered in the following sections.

>[AZURE.NOTE] It is advised that you read and understand the rest of this topic completely before starting an upgrade. Make note of any features you use which are called out below.

You can move and test your code at your pace. When the Mobile App backend is ready, you can release a new version of your client application. At this point, you will have two copies of your application backend running side by side. You need to make sure any bug fixes you make get applied to both. Finally, once your users have updated to the newest version, you can delete the original Mobile Service.

The full set of steps for this upgrade is as follows:

1. Create and configure a new Mobile App
2. Address any authentication concerns
3. Release a new version of your client application
4. Delete your original Mobile Services instance


##<a name="mobile-app-version"></a>Setting up a Mobile App version of your application
The first step in upgrading is to create the Mobile App resource which will host the new version of your application. You can create a new Mobile App in the [Preview Azure Management Portal]. You can consult the [Create a Mobile App] topic for further detail.

You will likely want to use the same database and Notification Hub as you did in Mobile Services. You can copy these values from the **Configure** tab of the Mobile Services section of the [Azure Management Portal]. Under **Connection Strings**, copy `MS_NotificationHubConnectionString` and `MS_TableConnectionString`. Navigate to your Mobile App site and select **Settings**, **Application settings**, and add these to the **Connection strings** section, overwriting any existing values.

Mobile Apps provides a new [Mobile App Server SDK] which provides much of the same functionality as the Mobile Services runtime. First, you should remove the Mobile Services NuGet from your existing project and instead include the Server SDK. For this upgrade, most developers will want to download install the `Microsoft.Azure.Mobile.Server.Quickstart` package, as this will pull in the entire required set. Then, in WebApiConfig.cs, you can replace 

    // Use this class to set configuration options for your mobile service
    ConfigOptions options = new ConfigOptions();
    
    // Use this class to set WebAPI configuration options
    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

with

    HttpConfiguration config = new HttpConfiguration();

    new MobileAppConfiguration()
	    .UseDefaultConfiguration()
	    .ApplyTo(config);


>[AZURE.NOTE] If you wish to learn more about the new .NET server SDK and how to add/remove features from your app, please see the [How to use the .NET server SDK] topic.

There are a few other changes between the Mobile Services and Mobile Apps SDKs, but they are easy to address. Throughout your project, you may need to modify some using statements, with which Visual Studio will assist.

You need to add the `[MobileAppController]` attribute to all of your ApiControllers, simply by placing that decorator directly before the class definition.

There is no longer an `[AuthorizeLevel]` attribute, and you should instead decorate your controllers and methods with the standard ASP.NET `[Authorize]` attribute. Note also that any controller that did not have an `[AuthorizeLevel]` is no longer protected by an application key; it will be treated as public. The new SDK no longer makes use of an Application Key and Master Key.

For push, the main item that you may find missing from the Server SDK is the PushRegistrationHandler class. Registrations are handled slightly differently in Mobile Apps, and tagless registrations are enabled by default. Managing tags may be accomplished by using custom APIs. Please see the [Add push notifications to your mobile app] topic for more information.

Scheduled jobs are not built into Mobile Apps, so any existing jobs that you have in your .NET backend will need to be upgraded individually. One option is to create a scheduled [Web Job] on the Mobile App code site. You could also set up a controller that holds your job code and configure the [Azure Scheduler] to hit that endpoint on the expected schedule.

The `ApiServices` object is no longer part of the SDK. To access Mobile App settings, you can use the following:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings(); 

Similarly, logging is now accomplished using the standard ASP.NET trace writing:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>Authentication considerations
One of the biggest differences between Mobile Apps and Mobile Services is that login is now handled by the App Service Gateway in the case of Mobile Apps, not the site running your code. If your resource group does not already have one, you can provision a gateway by navigating to your Azure Mobile App in the management portal. Then select **Settings**, and then **User authentication** under the **Mobile** category. Click **Create** to associate a gateway with your Mobile App.

Beyond this, most applications will require no additional action, but there are a few advanced scenarios which should be noted.

Most apps will be fine to simply use a new registration with the target identity provider, and you can learn about adding identity to an App Service app by following the [Add authentication to your mobile app] tutorial.

If your application takes dependencies on user IDs, such as storing them in a database, it is important to note that the user IDs between Mobile Services and App Service Mobile Apps are different. However, it is possible to get the Mobile Services User ID in your App Service Mobile App application by using the following (with Facebook as an example):

    MobileAppUser = (MobileAppUser) this.User;
    FacebookCredentials creds = await user.GetIdentityAsync<FacebookCredentials>();
    string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Additionally, if you take any dependencies on user IDs, it is important that you leverage the same registration with an identity provider  if possible. User IDs are typically scoped to the application registration that was used, so introducing a new registration could create problems with matching users to their data. Should your application need to use the same identity provider registration for any reason, you can use the following steps:

1. Copy over the Client ID and Client secret connection information for each provider used by your application.
2. Add the gateway's /signin-* endpoints as an additional redirect URI for each provider. 

>[AZURE.NOTE] Some providers, such as Twitter and Microsoft Account, do not allow you to specify multiple redirect URIs on different domains. If your app uses one of these providers and takes a dependency on user IDs, it is advised that you do not attempt to upgrade at this time.

##<a name="updating-clients"></a>Updating clients
Once you have an operational Mobile App backend, you can update your client application to consume the new Mobile App. Mobile Apps will also include a new version of the Mobile Services client SDKs, which will allow developers to take advantage of new App Service features. Once you have a Mobile App version of your backend, you can release a new version of your client application which leverages the new SDK version.

The main change that will be required of your client code is in the constructor. In addition to the URL of your Mobile App site, you need to provide the URL of the App Service Gateway which hosts your authentication settings:

    public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://contoso.azurewebsites.net", // URL of the Mobile App
        "https://contoso-gateway.azurewebsites.net", // URL of the App Service Gateway
        "" // Formerly app key. To be removed in future client SDK update
    );

This will allow the client to route requests to the components of your Mobile App. You can find more details specific to your target platform using the appropriate [Create a Mobile App] topic.

In the same update, you will need to adjust any push notification registration calls that you make. There are new APIs which make improvements to the registration process (using Windows as an example):

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    await MobileService.GetPush().Register(channel.Uri); 

Please see the [Add push notifications to your mobile app] and [Send cross-platform push notifications] topics for details specific to your target platform.

Once your customers have had a chance to receive these updates, you can delete the Mobile Services version of your app. At this point, you have completely upgraded to an App Service Mobile App using the latest Mobile Apps server SDK.

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
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[App Service pricing]: https://azure.microsoft.com/en-us/pricing/details/app-service/
