---
title: Waarden ophalen voor app-verificatie
description: Een service-principal maken voor toegang tot SQL Database vanuit code.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5b2c64660f37745f5b13d53559037e84ca20c47b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79476963"
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>De vereiste waarden ophalen voor het verifiëren van een toepassing voor toegang tot SQL Database vanuit code

Als u SQL Database wilt maken en beheren met code, moet u uw app registreren in het Azure Active Directory (AAD)-domein in het abonnement waar uw Azure-resources zijn gemaakt.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Een service-principal maken voor toegang tot resources vanuit een toepassing

In de volgende voor beelden worden de Active Directory (AD)-toepassing en de service-principal gemaakt die we nodig hebben om onze C#-app te verifiëren. Het script voert de waarden uit die we nodig hebben voor het voorgaande C#-voorbeeld. Zie [Use Azure PowerShell to create a service principal to access resources](../active-directory/develop/howto-authenticate-service-principal-powershell.md) (Azure PowerShell gebruiken om een service-principal te maken voor toegang tot resources) voor gedetailleerde informatie.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> De module Power shell Azure Resource Manager (RM) wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. De AzureRM-module blijft oplossingen ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek. Zie [Inleiding tot de nieuwe Azure PowerShell AZ-module](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit.

```powershell
# sign in to Azure
Connect-AzAccount

# for multiple subscriptions, uncomment and set to the subscription you want to work with
#$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
#Set-AzContext -SubscriptionId $subscriptionId

$appName = "{app-name}" # display name for your app, must be unique in your directory
$uri = "http://{app-name}" # does not need to be a real uri
$secret = "{app-password}"

# create an AAD app
$azureAdApplication = New-AzADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

# create a Service Principal for the app
$svcprincipal = New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

Start-Sleep -s 15 # to avoid a PrincipalNotFound error, pause here for 15 seconds

# if you still get a PrincipalNotFound error, then rerun the following until successful.
$roleassignment = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

# output the values we need for our C# application to successfully authenticate
Write-Output "Copy these values into the C# sample app"

Write-Output "_subscriptionId:" (Get-AzContext).Subscription.SubscriptionId
Write-Output "_tenantId:" (Get-AzContext).Tenant.TenantId
Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
Write-Output "_applicationSecret:" $secret
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
# sign in to Azure
az login

# for multiple subscriptions, uncomment and set to the subscription you want to work with
#$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
#az account set --subscription $subscriptionId

$appName = "{app-name}" # display name for your app, must be unique in your directory
$uri = "http://{app-name}" # does not need to be a real uri
$secret = "{app-password}"

# create an AAD app
$azureAdApplication = az ad app create --display-name $appName --homepage $Uri --identifier-uris $Uri --password $secret

# create a Service Principal for the app
$svcprincipal = az ad sp create --id $azureAdApplication.ApplicationId

Start-Sleep -s 15 # to avoid a PrincipalNotFound error, pause for 15 seconds

# if you still get a PrincipalNotFound error, then rerun the following until successful.
$roleassignment = az role assignment create --role "Contributor" --assignee $azureAdApplication.ApplicationId.Guid

# output the values we need for our C# application to successfully authenticate
Write-Output "Copy these values into the C# sample app"

Write-Output "_subscriptionId:" (az account show --query "id")
Write-Output "_tenantId:" (az account show --query "tenantId")
Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
Write-Output "_applicationSecret:" $secret
```

* * *

## <a name="see-also"></a>Zie ook

[Een SQL database maken met C #](sql-database-get-started-csharp.md)  
[Verbinding maken met SQL Database met behulp van Azure Active Directory-verificatie](sql-database-aad-authentication.md)
