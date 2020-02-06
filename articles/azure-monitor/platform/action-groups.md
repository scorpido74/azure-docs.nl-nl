---
title: Actie groepen maken en beheren in de Azure Portal
description: Meer informatie over het maken en beheren van actie groepen in de Azure Portal.
author: dkamstra
ms.topic: conceptual
ms.date: 8/19/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 01d3edb3de9e57fa7fa8db2ede863c2aa3e100ed
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030741"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Actie groepen maken en beheren in de Azure Portal
Een actie groep is een verzameling voor keuren voor meldingen die zijn gedefinieerd door de eigenaar van een Azure-abonnement. Azure Monitor-en Service Health-waarschuwingen gebruiken actie groepen om gebruikers te laten weten dat een waarschuwing is geactiveerd. Verschillende waarschuwingen kunnen dezelfde actie groep of verschillende actie groepen gebruiken, afhankelijk van de vereisten van de gebruiker. U kunt Maxi maal 2.000 actie groepen in een abonnement configureren.

U configureert een actie om een persoon op de hoogte te stellen per e-mail of SMS, waarna een bevestiging wordt gegeven dat de gebruiker aan de actie groep is toegevoegd.

In dit artikel wordt beschreven hoe u actie groepen maakt en beheert in de Azure Portal.

Elke actie bestaat uit de volgende eigenschappen:

* **Naam**: een unieke id in de actie groep.  
* **Actie type**: de uitgevoerde actie. Voor beelden hiervan zijn het verzenden van een spraak oproep, SMS, e-mail; of het activeren van verschillende typen geautomatiseerde acties. Zie typen verderop in dit artikel.
* **Details**: de bijbehorende details die per *actie type*verschillen.

Zie voor meer informatie over het gebruik van Azure Resource Manager sjablonen voor het configureren van actie groepen de [actie groep Resource Manager-sjablonen](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Een actie groep maken met behulp van de Azure Portal

1. Zoek in het [Azure Portal](https://portal.azure.com)naar en selecteer **monitor**. In het deel venster **monitor** worden al uw bewakings instellingen en-gegevens in één weer gave geconsolideerd.

1. Selecteer **Waarschuwingen** en vervolgens **Acties beheren**.

    ![Knop acties beheren](./media/action-groups/manage-action-groups.png)
    
1. Selecteer **actie groep toevoegen**en vul de velden in.

    ![De opdracht ' actie groep toevoegen '](./media/action-groups/add-action-group.png)
    
1. Voer een naam in het vak Naam van de **actie groep** in en voer een naam in het vak **korte naam** in. De korte naam wordt gebruikt in plaats van een volledige naam van de actiegroep als er meldingen via deze groep worden verzonden.

      ![Het dialoog venster actie groep toevoegen](./media/action-groups/action-group-define.png)

1. Het vak **abonnement** wordt aangevuld met uw huidige abonnement. Dit is de naam van het abonnement waarin de actie groep wordt opgeslagen.

1. Selecteer de **resource groep** waarin de actie groep wordt opgeslagen.

1. Definieer een lijst met acties. Geef het volgende op voor elke actie:

    1. **Naam**: Voer een unieke id in voor deze actie.

    1. **Actie type**: Selecteer E-mail/SMS/push/Voice, Logic app, webhook, ITSM of Automation Runbook.

    1. **Details**: Voer een telefoon nummer, e-mail adres, webhook-URI, Azure-app, ITSM-verbinding of Automation-runbook in op basis van het actie type. Geef voor ITSM-actie ook **werk item** en andere velden op die nodig zijn voor het ITSM-hulp programma.
    
    1. **Algemeen waarschuwings schema**: u kunt ervoor kiezen om het [algemene waarschuwings schema](https://aka.ms/commonAlertSchemaDocs)in te scha kelen. Dit biedt het voor deel van het gebruik van een single Extensible en Unified alert Payload in alle waarschuwings Services in azure monitor.

1. Selecteer **OK** om de actie groep te maken.

## <a name="manage-your-action-groups"></a>Uw actie groepen beheren

Nadat u een actie groep hebt gemaakt, kunt u **actie groepen** weer geven door **acties beheren** te selecteren op de pagina **waarschuwings signalen** in het deel venster **monitor** . Selecteer de actie groep die u wilt beheren:

* Acties toevoegen, bewerken of verwijderen.
* De actie groep verwijderen.

## <a name="action-specific-information"></a>Actie-specifieke informatie

> [!NOTE]
> Zie [limieten voor abonnements Services voor controle](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-monitor-limits) op numerieke limieten voor elk van de onderstaande items.  

### <a name="automation-runbook"></a>Automation-Runbook
Raadpleeg de [service limieten](../../azure-resource-manager/management/azure-subscription-service-limits.md) van het Azure-abonnement voor limieten voor nettoladingen van het Runbook.

Mogelijk hebt u een beperkt aantal Runbook-acties in een actie groep. 

### <a name="azure-app-push-notifications"></a>Push meldingen van Azure-app
Mogelijk hebt u een beperkt aantal Azure-app-acties in een actie groep.

### <a name="email"></a>Email
E-mails worden verzonden vanaf de volgende e-mail adressen. Controleren of uw e-mail filtering op de juiste wijze is geconfigureerd
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Mogelijk hebt u een beperkt aantal e-mail acties in een actie groep. Zie het artikel [informatie over de frequentie beperking](./../../azure-monitor/platform/alerts-rate-limiting.md) .

### <a name="email-azure-resource-manager-role"></a>E-mail Azure Resource Manager rol
E-mail verzenden naar de leden van de rol van het abonnement.

Mogelijk hebt u een beperkt aantal e-mail acties in een actie groep. Zie het artikel [informatie over de frequentie beperking](./../../azure-monitor/platform/alerts-rate-limiting.md) .

### <a name="function"></a>Functie
Hiermee wordt een bestaand HTTP trigger-eind punt aangeroepen in [Azure functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

Mogelijk hebt u een beperkt aantal functie acties in een actie groep.

### <a name="itsm"></a>ITSM
Voor de actie ITSM is een ITSM-verbinding vereist. Meer informatie over het maken van een [ITSM-verbinding](../../azure-monitor/platform/itsmc-overview.md).

Mogelijk hebt u een beperkt aantal ITSM-acties in een actie groep. 

### <a name="logic-app"></a>Logische apps
Mogelijk hebt u een beperkt aantal logische app-acties in een actie groep.

### <a name="secure-webhook"></a>Beveiligde webhook
**De functionaliteit van de beveiligde webhook is momenteel beschikbaar als preview-versie.**

Met de actie groepen webhook kunt u gebruikmaken van Azure Active Directory om de verbinding tussen uw actie groep en de beveiligde web-API (webhook-eind punt) te beveiligen. De algemene werk stroom voor het gebruik van deze functionaliteit wordt hieronder beschreven. Zie [overzicht van micro soft Identity platform (v 2.0)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)voor een overzicht van Azure AD-toepassingen en-service-principals.

1. Maak een Azure AD-toepassing voor uw beveiligde web-API. Zie https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Configureer uw beveiligde API om te worden aangeroepen door een daemon-app.
    
1. Schakel actie groepen in voor het gebruik van uw Azure AD-toepassing.

    > [!NOTE]
    > U moet lid zijn van de [rol Azure AD-toepassings beheerder](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) om dit script uit te voeren.
    
    - Wijzig de aanroep van de Connect-AzureAD van het Power shell-script om uw Azure AD-Tenant-ID te gebruiken.
    - De variabele $myAzureADApplicationObjectId van het Power shell-script wijzigen om de object-ID van uw Azure AD-toepassing te gebruiken
    - Voer het gewijzigde script uit.
    
1. Configureer de actie groep beveiligde webhooks.
    - Kopieer de waarde $myApp. ObjectId uit het script en voer deze in het veld toepassings object-ID in de actie definitie van webhook in.
    
    ![Actie beveiligde webhook](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Power shell-script voor beveiligde webhook

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
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
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>Sms
Zie de [informatie over het beperken](./../../azure-monitor/platform/alerts-rate-limiting.md) van de frequentie en het [gedrag van SMS-berichten](../../azure-monitor/platform/alerts-sms-behavior.md) voor aanvullende belang rijke informatie.

Mogelijk hebt u een beperkt aantal SMS-acties in een actie groep.  

### <a name="voice"></a>Spraak
Zie het artikel [informatie over de frequentie beperking](./../../azure-monitor/platform/alerts-rate-limiting.md) .

Mogelijk hebt u een beperkt aantal spraak acties in een actie groep.

### <a name="webhook"></a>Webhook
Webhooks worden opnieuw geprobeerd met de volgende regels. De webhook-aanroep wordt Maxi maal twee keer opnieuw geprobeerd wanneer de volgende HTTP-status codes worden geretourneerd: 408, 429, 503, 504 of het HTTP-eind punt reageert niet. De eerste poging vindt plaats na 10 seconden. De tweede nieuwe poging gebeurt na 100 seconden. Na twee storingen wordt het eind punt gedurende 30 minuten niet door een actie groep aangeroepen. 

IP-adresbereiken van bron
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Als u updates wilt ontvangen over wijzigingen in deze IP-adressen, raden we u aan een Service Health-waarschuwing te configureren, waarmee wordt gecontroleerd op informatieve meldingen over de service voor de actie groep.

Mogelijk hebt u een beperkt aantal webhook-acties in een actie groep.



## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [gedrag van SMS-waarschuwingen](../../azure-monitor/platform/alerts-sms-behavior.md).  
* Krijg [inzicht in het webhook-schema van waarschuwingen voor activiteiten logboeken](../../azure-monitor/platform/activity-log-alerts-webhook.md).  
* Meer informatie over [ITSM-connector](../../azure-monitor/platform/itsmc-overview.md)
* Meer informatie over de [frequentie limiet](../../azure-monitor/platform/alerts-rate-limiting.md) voor waarschuwingen.
* Bekijk een [overzicht van waarschuwingen voor activiteiten logboeken](../../azure-monitor/platform/alerts-overview.md)en meer informatie over het ontvangen van waarschuwingen.  
* Meer informatie over het [configureren van waarschuwingen wanneer een service status melding wordt geplaatst](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
