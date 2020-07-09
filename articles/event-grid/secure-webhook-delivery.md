---
title: Secure webhook-levering met Azure AD in Azure Event Grid
description: Hierin wordt beschreven hoe u gebeurtenissen kunt leveren aan HTTPS-eind punten die worden beveiligd door Azure Active Directory met behulp van Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: b0503d7da9e191e9d6764076392ead8faa5109a1
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119120"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Gebeurtenissen publiceren naar Azure Active Directory beveiligde eind punten

In dit artikel wordt beschreven hoe u Azure Active Directory kunt gebruiken om de verbinding tussen uw gebeurtenis abonnement en het eind punt van de webhook te beveiligen. Zie [overzicht van micro soft Identity platform (v 2.0)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)voor een overzicht van Azure AD-toepassingen en-service-principals.

In dit artikel wordt gebruikgemaakt van de Azure Portal voor demonstratie, maar de functie kan ook worden ingeschakeld met CLI, Power shell of de Sdk's.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-azure-ad-application"></a>Een Azure AD-toepassing maken

Maak eerst een Azure AD-toepassing voor uw beveiligde eind punt. Zie https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configureer uw beveiligde API om te worden aangeroepen door een daemon-app.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Event Grid inschakelen voor het gebruik van uw Azure AD-toepassing

Gebruik het onderstaande Power shell-script om een rol-en Service-Principal te maken in uw Azure AD-toepassing. U hebt de Tenant-ID en de object-ID van uw Azure AD-toepassing nodig:

   > [!NOTE]
   > U moet lid zijn van de [rol Azure AD-toepassings beheerder](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) om dit script uit te voeren.
    
1. Wijzig de $myTenantId van het Power shell-script om uw Azure AD-Tenant-ID te gebruiken.
1. Wijzig de $myAzureADApplicationObjectId van het Power shell-script om de object-ID van uw Azure AD-toepassing te gebruiken
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
    
Write-Host "My Azure AD Tenant Id: $myTenantId"
Write-Host "My Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Azure AD Application ObjectId: $($myApp.ObjectId)"
Write-Host "My Azure AD Application's Roles: "
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>Het gebeurtenis abonnement configureren

Selecteer in de stroom voor het maken van uw gebeurtenis abonnement het eindpunt type ' webhook '. Zodra u uw eind punt-URI hebt opgegeven, klikt u op het tabblad Extra functies boven aan de Blade gebeurtenis abonnementen maken.

![Type webhook voor eind punt selecteren](./media/secure-webhook-delivery/select-webhook.png)

Schakel op het tabblad Extra functies het selectie vakje ' AAD-verificatie gebruiken ' in en configureer de Tenant-ID en toepassings-ID:

* Kopieer de Azure AD-Tenant-ID uit de uitvoer van het script en voer deze in het veld AAD-Tenant-ID in.
* Kopieer de Azure AD-toepassings-ID uit de uitvoer van het script en voer deze in het veld AAD-toepassings-ID in.

    ![Actie beveiligde webhook](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Event grid bericht bezorging bewaken](monitor-event-delivery.md)voor meer informatie over het bewaken van gebeurtenis leveringen.
* Zie [Event grid beveiliging en verificatie](security-authentication.md)voor meer informatie over de verificatie sleutel.
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.
