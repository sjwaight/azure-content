<properties 
	pageTitle="Add push notifications to your Windows Runtime 8.1 universal app | Azure Mobile Apps" 
	description="Learn how to use Azure App Service Mobile Apps and Azure Notification Hubs to send push notifications to your Windows app." 
	services="app-service\mobile,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2015" 
	ms.author="glenga"/>

# Add push notifications to your Windows Runtime 8.1 universal app

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Overview

This topic shows you how to send push notifications to a Windows Runtime 8.1 universal app by using Azure App Service Mobile Apps and Azure Notification Hubs. In this scenario, when a new item is added, your Mobile App backend sends a push notification to all Windows apps registered with the Windows Notification Service (WNS).

This tutorial is based on the App Service Mobile App quickstart. Before you start this tutorial, you must first complete the quickstart tutorial [Create a Windows app](../app-service-mobile-windows-store-dotnet-get-started.md). If you do not use the downloaded quick start server project, you must add the push notification extension package to your project. For more information about server extension packages, see [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md). 

##Prerequisites

To complete this tutorial, you need the following:

* An active [Microsoft Store account](http://go.microsoft.com/fwlink/p/?LinkId=280045).
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934)
* Complete the [quickstart tutorial](../app-service-mobile-windows-store-dotnet-get-started.md).  


##<a name="create-hub"></a>Create a Notification Hub

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##Register your app for push notifications

Before you can send push notifications to your Windows apps from Azure, you must submit your app to the Windows Store. You can then configure your server project to integrate with WNS.

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


##Configure Mobile App to send push requests

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


##<a id="update-service"></a>Update the server to send push notifications

Now that push notifications are enabled in the app, you must update your app backend to send push notifications. 

1. In Visual Studio, right-click the server project and click **Manage NuGet Packages**, search for `Microsoft.Azure.NotificationHubs`, then click **Install**. This installs the Notification Hubs client library.

2. In the server project, open **Controllers** > **TodoItemController.cs**, and add the following using statements:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;

3. In the **PostTodoItem** method, add the following code after the call to **InsertAsync**:  

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
			this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

		// Define a WNS payload
		var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" 
                                + item.Text + @"</text></binding></visual></toast>";

        try
        {
			// Send the push notification and log the results.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    This code tells the notification hub to send a push notification after a new item is insertion.


## <a name="publish-the-service"></a>Publish the mobile backend to Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##<a id="update-service"></a>Add push notifications to your app

1. Open the shared **App.xaml.cs** project file and add the following `using` statements:

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

2. In the same file, add the following **InitNotificationsAsync** method definition to the **App** class:
    
        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    This code retrieves the ChannelURI for the app from WNS, and then registers that ChannelURI with your App Service Mobile App.
    
3. At the top of the **OnLaunched** event handler in **App.xaml.cs**, add the **async** modifier to the method definition and add the following call to the new **InitNotificationsAsync** method, as in the following example:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    This guarantees that the short-lived ChannelURI is registered each time the application is launched.

4. In Solution Explorer double-click **Package.appxmanifest** of the Windows Store app, in **Notifications**, set **Toast capable** to **Yes**.

    From the **File** menu, click **Save All**.

5. Repeat the previous step in the Windows Phone Store app project.

Your app is now ready to receive toast notifications.

##<a id="test"></a>Test push notifications in your app

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

##<a id="more"></a>More

* Templates give you flexibility to send cross-platform pushes and localized pushes. [How to use the managed client for Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md) shows you how to register templates.
* Tags allow you to target segmented customers with pushes. [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) shows you how to add tags to a device installation.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->