<properties
   pageTitle="Create AD application and service principal in portal | Microsoft Azure"
   description="Describes how to create a new Active Directory application and service principal that can be used with the role-based access control in Azure Resource Manager to manage access to resources."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/17/2015"
   ms.author="tomfitz"/>

# Create Active Directory application and service principal using portal

## Overview
When you have an automated process or application that needs to access or modify a resource in your subscription, you can use the portal to create an Active Directory application and assign it to a role with the correct permission. When you create an Active Directory application through the portal, it actually creates both the application and a service principal. You use the service principal when setting the permissions.

This topic shows you how to create a new application and service principal using the Azure portal. Currently, you must use the Microsoft Azure portal to create a new Active Directory application. This ability will be added to the Azure preview portal in a later release. You can use the preview portal to assign the application to a role. You can also perform these steps through Azure PowerShell or Azure CLI. For more information, see [Authenticating a service principal with Azure Resource Manager](resource-group-authenticate-service-principal.md).

## Concepts
1. Azure Active Directory (AAD) - an identity and access management service build for the cloud. For more details see: [What is Azure active Directory](active-directory/active-directory-whatis.md)
2. Service Principal - an instance of an application in a directory.
3. AD Application - a directory record in AAD that identifies an application to AAD. 

For a more detailed explanation of applications and service principals, see [Application Objects and Service Principal Objects](active-directory/active-directory-application-objects.md). 
For more information about Active Directory authentication, see [Authentication Scenarios for Azure AD](active-directory/active-directory-authentication-scenarios.md).


## Create the application and service principal objects

1. Login to your Azure Account through the [portal](https://manage.windowsazure.com/).

2. Select **Active Directory** from the left pane.

     ![select Active Directory][1]

3. Select the directory that you want to use for creating the new application.

     ![choose directory][2]

3. To view the applications in your directory, click on **Applications**.

     ![view applications][11]

4. If you haven't created an application in that directory before you should see something similar to following image. Click on **ADD AN APPLICATION**

     ![add application][6]

     Or, click **Add** in the bottom pane.

     ![add][12]

5. Select the type of application you would like to create. For this tutorial, we will not use an application from the gallery.

     ![new application][10]

6. Fill in name of the application and select the type of application you want to use. Since we intend to use this application's service principal to authenticate with Azure Resource Manager, we will elect to create a **WEB APPLICATION AND/OR WEB API** and click the next button.

     ![name application][9]

7. Fill in the properties for your app. For **SIGN-ON URL**, provide the URI to a web-site that describes your application. The existence of the web-site is not validated. 
For **APP ID URI**, provide the URI that identifies your application. The uniqueness or existence of the endpoint is not validated. Click the **Complete** to create you AAD Application.

     ![application properties][4]

## Create an authentication key for your application
The portal should now have your application selected.

1. Click on the **Configure** tab to configure your application's password.

     ![configure application][3]

2. Scroll down to the **Keys** section and select how long you would like your password to be valid.

     ![keys][7]

3. Select **Save** to create your key.

     ![save][13]

     The saved key is displayed and you can copy it. You will not be able to retrieve the key later so you will want to copy it now.

     ![saved key][8]

4. You can now use you key to authenticate as a service principal. You will need your **CLIENT ID** in addition to your **KEY** to sign in. Go to **CLIENT ID** and copy it.
  
     ![client id][5]

5. In some cases, you need to pass the tenant id with your authentication request. You can retrieve the tenant id by selecting **View endpoints** and retrieving the id as shown below.

     ![tenant id](./media/resource-group-create-service-principal-portal/save-tenant.png)

Your application is now ready and the service principal created on your tenant. When signing in as a service principal be sure to use:

* **CLIENT ID** - as your user name.
* **KEY** - as your password.

## Assigning the application to a role

You must assign the application to a role to grant it permissions for performing actions. You can use the [preview portal](https://portal.azure.com) to assign the Active Directory application to a role with the correct permissions.

To get started with access control in the preview portal, select the **Access** icon.

![select users](./media/resource-group-create-service-principal-portal/select-users.png)

Select the role you wish to assign the application to, and search for the application.

![select users](./media/resource-group-create-service-principal-portal/assign-to-role.png)

For more information about working with users, applications and roles, see [Manage access using the Azure Management Portal](active-directory/role-based-access-control-configure/#manage-access-using-the-azure-management-portal).

## Get access token in code

If you are using .NET, you can retrieve the access token for your application with the following code.

First, you must install the Active Directory Authentication Library into your Visual Studio project. The easiest way to do this is to use the NuGet package. Open the Package Manager Console, and type the following commands.

    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    PM> Update-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Safe

In your application, add a method like the following to retrieve the token.

    public static string GetAccessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
        var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

## Next Steps

- To learn about specifying security policies, see [Managing and Auditing Access to Resources](resource-group-rbac.md).  
- For a video demonstration of these steps, see [Enabling Programmatic Management of an Azure Resource with Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).
- To learn about using Azure PowerShell or Azure CLI to work with Active Directory applications and service principals, including how to use a certificate for authentication, see [Authenticating a Service Principal with Azure Resource Manager](./resource-group-authenticate-service-principal.md).
- For guidance on implementing security with Azure Resource Manager, see [Security considerations for Azure Resource Manager](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-create-service-principal-portal/active-directory.png
[2]: ./media/resource-group-create-service-principal-portal/active-directory-details.png
[3]: ./media/resource-group-create-service-principal-portal/application-configure.png
[4]: ./media/resource-group-create-service-principal-portal/app-properties.png
[5]: ./media/resource-group-create-service-principal-portal/client-id.png
[6]: ./media/resource-group-create-service-principal-portal/create-application.png
[7]: ./media/resource-group-create-service-principal-portal/create-key.png
[8]: ./media/resource-group-create-service-principal-portal/save-key.png
[9]: ./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png
[10]: ./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png
[11]: ./media/resource-group-create-service-principal-portal/view-applications.png
[12]: ./media/resource-group-create-service-principal-portal/add-icon.png
[13]: ./media/resource-group-create-service-principal-portal/save-icon.png
