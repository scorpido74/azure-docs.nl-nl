---
title: Wijzigingen in de delegatie in uw beheertenant controleren
description: Meer informatie over het controleren van delegatieactiviteiten van klanthuurders naar uw beherende tenant.
ms.date: 03/16/2020
ms.topic: conceptual
ms.openlocfilehash: 99aa05cb73326e441c0473855c27dc71212cf415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478225"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Wijzigingen in de delegatie in uw beheertenant controleren

Als serviceprovider wilt u mogelijk weten wanneer klantabonnementen of resourcegroepen worden gedelegeerd aan uw tenant via [azure-gedelegeerd bronbeheer](../concepts/azure-delegated-resource-management.md)of wanneer eerder gedelegeerde resources worden verwijderd.

In de beheertenant houdt het [Azure-activiteitenlogboek](../../azure-monitor/platform/platform-logs-overview.md) delegatieactiviteiten bij op tenantniveau. Deze aangemelde activiteit omvat eventuele toegevoegde of verwijderde delegaties van alle huurders van klanten.

In dit onderwerp worden de machtigingen uitgelegd die nodig zijn om de activiteiten van de delegatie naar uw tenant (voor al uw klanten) en de aanbevolen procedures hiervoor te controleren. Het bevat ook een voorbeeldscript dat één methode voor het opvragen en rapporteren van deze gegevens weergeeft.

> [!IMPORTANT]
> Al deze stappen moeten worden uitgevoerd in uw beherende huurder, in plaats van in een klant huurders.

## <a name="enable-access-to-tenant-level-data"></a>Toegang tot gegevens op tenantniveau inschakelen

Als u toegang wilt krijgen tot activiteitslogboekgegevens op tenantniveau, moet aan een account de ingebouwde rol [Monitoringreader](../../role-based-access-control/built-in-roles.md#monitoring-reader) bij het hoofdbereik (/) worden toegewezen. Deze toewijzing moet worden uitgevoerd door een gebruiker die de rol Globale beheerder heeft met extra verhoogde toegang.

### <a name="elevate-access-for-a-global-administrator-account"></a>Toegang voor een Global Administrator-account verhogen

Als u een rol wilt toewijzen aan het hoofdbereik (/), moet u de rol Globale beheerder met verhoogde toegang hebben. Deze verhoogde toegang mag alleen worden toegevoegd wanneer u de roltoewijzing moet maken en vervolgens moet worden verwijderd wanneer u klaar bent.

Zie Toegang opheffen om alle [Azure-abonnementen en beheergroepen te beheren voor](../../role-based-access-control/elevate-access-global-admin.md)gedetailleerde instructies over het toevoegen en verwijderen van hoogte.

Nadat u uw toegang hebt verhoogd, heeft uw account de functie Gebruikerstoegangsbeheerder in Azure op hoofdbereik. Met deze roltoewijzing u alle bronnen bekijken en toegang toewijzen in een abonnement of beheergroep in de map, en roltoewijzingen maken op basisvan het hoofdbereik. 

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>Een nieuw servicehoofdaccount maken om toegang te krijgen tot gegevens op tenantniveau

Nadat u uw toegang hebt verhoogd, u de juiste machtigingen toewijzen aan een account, zodat het activiteitslogboekgegevens op tenantniveau kan opvragen. Dit account moet de ingebouwde rol [van de monitoringlezer](../../role-based-access-control/built-in-roles.md#monitoring-reader) hebben toegewezen aan de hoofdscope van uw beheertenant.

> [!IMPORTANT]
> Het verlenen van een roltoewijzing bij rootscope betekent dat dezelfde machtigingen van toepassing zijn op elke resource in de tenant.

Omdat dit een breed toegangsniveau is, raden we u aan deze rol toe te wijzen aan een hoofdaccount voor services, in plaats van aan een individuele gebruiker of aan een groep. Daarnaast raden we de volgende aanbevolen procedures aan:

- [Maak een nieuw serviceprincipal-account](../../active-directory/develop/howto-create-service-principal-portal.md) dat alleen voor deze functie kan worden gebruikt, in plaats van deze rol toe te kennen aan een bestaande serviceprincipal die wordt gebruikt voor andere automatisering.
- Zorg ervoor dat deze serviceprincipal geen toegang heeft tot gedelegeerde klantbronnen.
- [Gebruik een certificaat om](../../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets) het te verifiëren en veilig [op te slaan in Azure Key Vault.](../../key-vault/key-vault-best-practices.md)
- Beperk de gebruikers die toegang hebben om namens de serviceprincipal op te treden.

Gebruik een van de volgende methoden om de rootscope-toewijzingen uit te voeren.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ApplicationId $servicePrincipal.ApplicationId 
```

#### <a name="azure-cli"></a>Azure-CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Verhoogde toegang voor het Global Administrator-account verwijderen

Nadat u uw serviceprincipal-account hebt gemaakt en de rol Monitoring Reader bij het hoofdbereik hebt toegewezen, moet u de verhoogde toegang voor het Global Administrator-account [verwijderen,](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) omdat dit toegangsniveau niet langer nodig is.

## <a name="query-the-activity-log"></a>Het activiteitenlogboek opvragen

Zodra u een nieuw serviceprincipal-account hebt gemaakt met Monitoring Reader-toegang tot het hoofdbereik van uw beheertenant, u deze gebruiken om de activiteiten van de delegatie in uw tenant op te vragen en te rapporteren. 

In het onderstaande voorbeeld wordt Azure PowerShell gebruikt om de afgelopen 1 dag van activiteit en rapporten op te vragen over toegevoegde of verwijderde delegaties (of pogingen die niet zijn gelukt). Het query's de [tenant activiteit log](https://docs.microsoft.com/rest/api/monitor/TenantActivityLogs/List) gegevens, dan construeert de volgende waarden te rapporteren over delegaties die worden toegevoegd of verwijderd:

- **Gedelegeerdresourceid:** de id van het gedelegeerde abonnement of de resourcegroep
- **CustomerTenantId**: De klanttenant-id
- **CustomerSubscriptionId**: de abonnements-id die is gedelegeerd of die de brongroep bevat die is gedelegeerd
- **Status van klantdelegatie:** de statuswijziging voor de gedelegeerde resource (geslaagd of mislukt)
- **EventTimeStamp**: de datum en het tijdstip waarop de wijziging van de delegatie is geregistreerd

Houd bij het opvragen van deze gegevens rekening met:

- Als meerdere resourcegroepen in één implementatie worden gedelegeerd, worden afzonderlijke vermeldingen geretourneerd voor elke resourcegroep.
- Wijzigingen in een vorige delegatie (zoals het bijwerken van de machtigingsstructuur) worden geregistreerd als een toegevoegde delegatie.
- Zoals hierboven vermeld, moet een account de ingebouwde rol van de monitoringlezer hebben bij het hoofdbereik (/) om toegang te krijgen tot deze tenantgegevens.
- U deze gegevens gebruiken in uw eigen workflows en rapportage. U bijvoorbeeld de [HTTP Data Collector API (public preview)](../../azure-monitor/platform/data-collector-api.md) gebruiken om gegevens te registreren bij Azure Monitor vanuit een REST API-client en vervolgens [actiegroepen](../../azure-monitor/platform/action-groups.md) te gebruiken om meldingen of waarschuwingen te maken.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the past 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method = 'GET'
}
$list = Invoke-RestMethod @listOperations
$showOperations = $list.value

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action")
{
    $registerOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action"}
    foreach ($registerOutput in $registerOutputs)
    {
    $registerOutputdata = [pscustomobject]@{
        Event = "An Azure customer has delegated resources to your tenant";
        DelegatedResourceId = $registerOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $registerOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $registerOutput.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") 
{
    $unregisterOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action"}
    foreach ($unregisterOutput in $unregisterOutputs)
    {
    $unregisterOutputdata = [pscustomobject]@{
        Event = "An Azure customer has removed delegated resources from your tenant";
        DelegatedResourceId = $unregisterOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $unregisterOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $unregisterOutput.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else 
{
    Write-Output "No new delegation changes."
}   


```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het aan boord nemen van klanten voor [Azure delegated resource management](../concepts/azure-delegated-resource-management.md).
- Meer informatie over [Azure Monitor](../../azure-monitor/index.yml) en het [Azure-activiteitenlogboek](../../azure-monitor/platform/platform-logs-overview.md).
