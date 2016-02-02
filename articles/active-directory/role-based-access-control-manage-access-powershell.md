<properties
	pageTitle="Manage Role Based Access Control (RBAC) with Azure PowerShell | Microsoft Azure"
	description="How to manage RBAC with Azure PowerShell including listing roles, assigning roles and deleting role assignments."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="10/12/2015"
	ms.author="inhenk"/>

# Manage Role Based Access Control (RBAC) with Azure PowerShell
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## List RBAC roles
### List all available roles
To list RBAC roles available for assignment and to inspect the operations to which they grant access use:

		Get-AzureRoleDefinition

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### List actions of a role
To list the actions for a specific role use:

    Get-AzureRoleDefinition <role name>

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## List Access
### List all role assignments in the selected subscription
To list RBAC access assignments effective at the specified subscription, resource, or resource group use:

    Get-AzureRoleAssignment

###	List role assignments effective on a resource group
To list the access assignments for a resource group use:

    Get-AzureRoleAssignment -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### List role assignments to a user, including ones assigned to users groups
To list access assignments to the specified user as well as to the groups of which the user is member use:

    Get-AzureRoleAssignment -ExpandPrincipalGroups

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### List classic service administrator and co-admin role assignments
To list access assignments for the classic subscription administrator and co-administrators use:

    Get-AzureRoleAssignment -IncludeClassicAdministrators

## Grant Access
### Search for object ids
In order to use the following command sequences, you must find the object ids first.  It is assumed that you already know the subscription id that you are working with, otherwise see [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) on MSDN.

#### Find the object id for an Azure AD Group
To get the object id for an Azure AD Group use:

    Get-AzureADGroup -SearchString <group name in quotes>

#### Find the object id for an Azure AD Service Principal
To get the object id for an Azure AD Service Principal use:
    Get-AzureADServicePrincipal -SearchString <service name in quotes>

### Assign role to group at subscription scope
To grant access to a group at subscription scope use:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### Assign role to application at subscription scope
To grant access to an application at subscription scope use:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### Assign role to user at resource group scope
To grant access to a user at resource group scope:

    New-AzureRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### Assign role to group at resource scope
To grant access to a group at the resource scope use:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## Remove access
To remove access for users, groups and applications use:

    Remove-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## RBAC topics
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]
