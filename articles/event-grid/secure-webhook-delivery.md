---
title: Secure WebHook delivery with Azure AD in Azure Event Grid
description: Beschrijft hoe u gebeurtenissen leveren aan HTTPS-eindpunten die zijn beveiligd door Azure Active Directory met Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 074378668b0516936e11968ea8c800d3daa667bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931542"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Gebeurtenissen publiceren naar azure Active Directory-beveiligde eindpunten

In dit artikel wordt beschreven hoe u gebruik maken van Azure Active Directory om de verbinding tussen uw gebeurtenisabonnement en uw webhook-eindpunt te beveiligen. Zie overzicht van [Microsoft Identity Platform (v2.0)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)voor een overzicht van Azure AD-toepassingen en serviceprincipals.

In dit artikel wordt de Azure-portal gebruikt voor demonstratie, maar de functie kan ook worden ingeschakeld met CLI, PowerShell of de SDK's.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-azure-ad-application"></a>Een Azure AD-toepassing maken

Begin met het maken van een Azure AD-toepassing voor uw beveiligde eindpunt. Zie https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configureer uw beveiligde API om te worden aangeroepen door een daemon-app.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Gebeurtenisraster inschakelen om uw Azure AD-toepassing te gebruiken

Gebruik het Onderstaande PowerShell-script om een rol- en serviceprincipe te maken in uw Azure AD-toepassing. U hebt de tenant-id en object-id van uw Azure AD-toepassing nodig:

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
1. Wijzig de $myTenantId van het PowerShell-script om uw Azure AD-tenant-id te gebruiken.
1. De $myAzureADApplicationObjectId van het PowerShell-script wijzigen om de object-id van uw Azure AD-toepassing te gebruiken
1. Voer het gewijzigde script uit.

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"

Connect-AzureAD -TenantId $myTenantId
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
Write-Host "My Azure AD Tenant Id" + $myTenantId
Write-Host "My Azure AD Application Id" + $myAzureADApplicationObjectId
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>Het gebeurtenisabonnement configureren

Selecteer eindpunttype 'Webhook' in de creatiestroom voor uw evenementabonnement. Zodra u uw eindpunt URI hebt gegeven, klikt u op het tabblad Extra functies boven aan het blad voor gebeurtenisabonnementen maken.

![Webhook van eindpunttype selecteren](./media/secure-webhook-delivery/select-webhook.png)

Schakel op het tabblad Extra functies het selectievakje 'AAD-verificatie gebruiken' in en configureer de tenant-id en toepassings-id:

* Kopieer de Azure AD-tenant-id uit de uitvoer van het script en voer deze in het veld AAD Tenant-id in.
* Kopieer de Azure AD-toepassings-id uit de uitvoer van het script en voer deze in het veld AAD-toepassings-id in.

    ![Veilige Webhook-actie](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Gebeurtenisnetbezorging controleren](monitor-event-delivery.md)voor informatie over het bewaken van gebeurtenisleveringen.
* Zie [Beveiliging en verificatie van gebeurtenisraster](security-authentication.md)voor meer informatie over de verificatiesleutel.
* Zie [Abonnement op gebeurtenisrastervoor](subscription-creation-schema.md)meer informatie over het maken van een Azure Event Grid-abonnement .
