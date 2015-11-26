<properties 
	pageTitle="Add push notifications to your Xamarin.iOS app with Azure App Service" 
	description="Learn how to use Azure App Service to send push notifications to your Xamarin.iOS app" 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="11/23/2015" 
	ms.author="wesmc"/>

# Add push notifications to your Xamarin.iOS App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Overview

This tutorial is based on the [Xamarin.iOS quickstart tutorial](app-service-mobile-xamarin-ios-get-started.md), which you must complete first. You will add push notifications to the Xamarin.iOS quick start project so that every time a record is inserted, a push notification is sent. If you do not use the downloaded quick start server project, you must add the push notification extension package to your project. For more information about server extension packages, see [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md). 

The [iOS simulator does not support push notifications](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), so you must use a physical iOS device. You'll also need to sign up for an [Apple Developer Program membership](https://developer.apple.com/programs/ios/).

##Prerequisites

To complete this tutorial, you need the following:

* An active Azure account.  
If you don't have an account yet, sign up for an Azure trial and get up to 10 free mobile apps. You can keep using them even after your trial ends. See [Azure Free Trial](http://azure.microsoft.com/pricing/free-trial/).

* A Mac with [Xamarin Studio] and [Xcode] v4.4 or later installed it. You can run the Xamarin.iOS app using Visual Studio on a Windows computer if you want, but it's a bit more complicated because you have to connect to a networked Mac running the Xamarin.iOS Build Host. If you're interested in doing that, see [Installing Xamarin.iOS on Windows].

* A physical iOS device. Push notifications are not supported by the iOS simulator.

* Complete the [Xamarin.iOS quickstart tutorial](app-service-mobile-xamarin-ios-get-started.md).


##Register the app for push notifications on Apple's developer portal

[AZURE.INCLUDE [Notification Hubs Xamarin Enable Apple Push Notifications](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]

##Configure your Mobile App to send push notifications

To configure your app to send notifications, create a new hub and configure it for the platform notification services that you will use.  

1. In the Azure portal, click **Browse** > **Mobile Apps** > your Mobile App > **Settings** > **Mobile** > **Push** > **Notification Hub** > **+ Notification Hub**, and provide a name and namespace for a new notification hub, and then click the **OK** button.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. In the Create Notification Hub blade, click **Create**.

3. Click **Push** > **Apple (APNS)** > **Upload Certificate**. Upload the .p12 push certificate file you exported earlier.  Make sure to select **Sandbox** if you created a development push certificate for development and testing.  Otherwise, choose **Production**. Your service is now configured to work with push notifications on iOS!

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)

##Update the server project to send push notifications

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##Deploy server project to Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##Configure your Xamarin.Forms project

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

##Add push notifications to your app

1. In **QSTodoService**, add the following property so that **AppDelegate** can acquire the mobile client:
        
            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. Add the following `using` statement to the top of the **AppDelegate.cs** file.

        using Microsoft.WindowsAzure.MobileServices;

2. In **AppDelegate**, override the **FinishedLaunching** event: 

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert 
                | UIUserNotificationType.Badge 
                | UIUserNotificationType.Sound, 
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. In the same file, override the **RegisteredForRemoteNotifications** event:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken);
        }

4. Then, override the **DidReceivedRemoteNotification** event:

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

Your app is now updated to support push notifications.

## <a name="test"></a>Test push notifications in your app

1. Press the **Run** button to build the project and start the app in an iOS capable device, then click **OK** to accept push notifications.
	
	> [AZURE.NOTE] You must explicitly accept push notifications from your app. This request only occurs the first time that the app runs.

2. In the app, type a task, and then click the plus (**+**) icon.

3. Verify that a notification is received, then click **OK** to dismiss the notification.

4. Repeat step 2 and immediately close the app, then verify that a notification is shown.

You have successfully completed this tutorial.

<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333


 