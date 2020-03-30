---
title: Rollen, machtigingen en beveiliging in Azure Monitor
description: Meer informatie over het gebruik van de ingebouwde rollen en machtigingen van Azure Monitor om de toegang tot bewakingsbronnen te beperken.
author: johnkemnetz
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 81309f0b5781e6302887a5b079ed359e70659834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658962"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Rollen, machtigingen en beveiliging in Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Veel teams moeten de toegang tot bewakingsgegevens en -instellingen strikt reguleren. Als u bijvoorbeeld teamleden hebt die uitsluitend werken aan monitoring (support engineers, DevOps engineers) of als u een managed service provider gebruikt, u hen toegang verlenen tot alleen monitoringgegevens terwijl ze hun mogelijkheid beperken om te maken, te wijzigen of resources verwijderen. In dit artikel ziet u hoe u snel een ingebouwde controle-RBAC-rol toepast op een gebruiker in Azure of uw eigen aangepaste rol bouwen voor een gebruiker die beperkte controlemachtigingen nodig heeft. Vervolgens worden beveiligingsoverwegingen voor uw Azure Monitor-gerelateerde bronnen besproken en hoe u de toegang tot de gegevens die ze bevatten beperken.

## <a name="built-in-monitoring-roles"></a>Ingebouwde bewakingsrollen
De ingebouwde rollen van Azure Monitor zijn ontworpen om de toegang tot resources in een abonnement te beperken en tegelijkertijd degenen die verantwoordelijk zijn voor het bewaken van de infrastructuur de gegevens te verkrijgen en te configureren die ze nodig hebben. Azure Monitor biedt twee out-of-the-box rollen: een monitoringreader en een monitoringbijdrager.

### <a name="monitoring-reader"></a>Monitoring Reader
Personen die de rol MonitoringReader hebben toegewezen, kunnen alle bewakingsgegevens in een abonnement weergeven, maar kunnen geen resource wijzigen of instellingen bewerken die verband houden met bewakingsbronnen. Deze rol is geschikt voor gebruikers in een organisatie, zoals ondersteunings- of operations engineers, die:

* Bekijk monitoringdashboards in de portal en maak hun eigen privémonitoringdashboards.
* Waarschuwingsregels weergeven die zijn gedefinieerd in [Azure-waarschuwingen](alerts-overview.md)
* Query voor metrische gegevens met behulp van de [Azure Monitor REST API,](https://msdn.microsoft.com/library/azure/dn931930.aspx) [PowerShell-cmdlets](powershell-quickstart-samples.md)of [cross-platform CLI](cli-samples.md).
* Query erin het activiteitenlogboek met behulp van de portal, Azure Monitor REST API, PowerShell-cmdlets of cross-platform CLI.
* Bekijk de [diagnostische instellingen](diagnostic-settings.md) voor een resource.
* Bekijk het [logboekprofiel](activity-log-export.md) voor een abonnement.
* Instellingen voor automatische schaal weergeven.
* Bekijk waarschuwingsactiviteit en -instellingen.
* Toegang tot Application Insights-gegevens en bekijk gegevens in AI Analytics.
* Zoek naar log Analytics-werkruimtegegevens, inclusief gebruiksgegevens voor de werkruimte.
* Beheergroepen van Log Analytics weergeven.
* Het zoekschema ophalen in de werkruimte Log Analytics.
* Lijstcontrolepakketten in de werkruimte Log Analytics.
* Opgeslagen zoekopdrachten ophalen en uitvoeren in de werkruimte Log Analytics.
* De opslagconfiguratie van Log Analytics-werkruimte ophalen.

> [!NOTE]
> Deze rol geeft geen leestoegang tot logboekgegevens die zijn gestreamd naar een gebeurtenishub of zijn opgeslagen in een opslagaccount. [Zie hieronder](#security-considerations-for-monitoring-data) voor informatie over het configureren van toegang tot deze bronnen.
> 
> 

### <a name="monitoring-contributor"></a>Monitorbijdrager
Personen die de rol Monitorbijdrager hebben toegewezen, kunnen alle bewakingsgegevens in een abonnement weergeven en bewakingsinstellingen maken of wijzigen, maar kunnen geen andere bronnen wijzigen. Deze rol is een superset van de rol Monitoring Reader en is geschikt voor leden van het monitoringteam van een organisatie of managed service providers die, naast de bovenstaande machtigingen, ook in staat moeten zijn om:

* Publiceer monitoringdashboards als een gedeeld dashboard.
* [Stel diagnostische instellingen](diagnostic-settings.md) in voor een resource.\*
* Stel het [logboekprofiel](activity-log-export.md) in voor een abonnement.\*
* Stel activiteit en instellingen voor waarschuwingsregels in via [Azure Alerts](alerts-overview.md).
* Maak webtests en componenten van Application Insights.
* Gedeelde sleutels van de Log Analytics-werkruimte weergeven.
* Bewakingspakketten in- of uitschakelen in de werkruimte Log Analytics.
* Opgeslagen zoekopdrachten maken en verwijderen en uitvoeren in de werkruimte Log Analytics.
* Maak en verwijder de configuratie van de log Analytics-werkruimteopslag.

\*de gebruiker moet ook afzonderlijk toestemming van ListKeys krijgen op de doelbron (opslagaccount of naamruimte van gebeurtenishub) om een logboekprofiel of diagnostische instelling in te stellen.

> [!NOTE]
> Deze rol geeft geen leestoegang tot logboekgegevens die zijn gestreamd naar een gebeurtenishub of zijn opgeslagen in een opslagaccount. [Zie hieronder](#security-considerations-for-monitoring-data) voor informatie over het configureren van toegang tot deze bronnen.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Machtigingen en aangepaste RBAC-rollen voor bewaking
Als de bovenstaande ingebouwde rollen niet voldoen aan de exacte behoeften van uw team, u [een aangepaste RBAC-rol maken](../../role-based-access-control/custom-roles.md) met meer gedetailleerde machtigingen. Hieronder vindt u de algemene Azure Monitor RBAC-bewerkingen met hun beschrijvingen.

| Bewerking | Beschrijving |
| --- | --- |
| Microsoft.Insights/actiegroepen/[Lezen, schrijven, verwijderen] |Actiegroepen lezen/schrijven/verwijderen. |
| Microsoft.Insights/ActivityLogAlerts/[Lezen, schrijven, verwijderen] |Waarschuwingen voor activiteitenlogboeken lezen/schrijven/verwijderen. |
| Microsoft.Insights/Waarschuwingsregels/[Lezen, schrijven, verwijderen] |Waarschuwingsregels lezen/schrijven/verwijderen (uit waarschuwingen klassiek). |
| Microsoft.Insights/Waarschuwingsregels/Incidenten/Lezen |Lijst incidenten (geschiedenis van de waarschuwingsregel wordt geactiveerd) voor waarschuwingsregels. Dit geldt alleen voor het portaal. |
| Microsoft.Insights/AutoscaleSettings/[Lezen, schrijven, verwijderen] |Instellingen voor automatisch schalen lezen/schrijven/verwijderen. |
| Microsoft.Insights/DiagnosticSettings/[Lezen, schrijven, verwijderen] |Diagnostische instellingen lezen/schrijven/verwijderen. |
| Microsoft.Insights/EventCategories/Read |Alle categorieën opsommen die mogelijk zijn in het activiteitenlogboek. Wordt gebruikt door de Azure-portal. |
| Microsoft.Insights/eventtypes/digestevents/Read |Deze toestemming is nodig voor gebruikers die via de portal toegang nodig hebben tot activiteitslogboeken. |
| Microsoft.Insights/eventtypes/waarden/Lezen |Activiteitslogboekgebeurtenissen (beheergebeurtenissen) in een abonnement weergeven. Deze toestemming is van toepassing op zowel programmatische als portaltoegang tot het activiteitenlogboek. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Lezen, schrijven, verwijderen] | Diagnostische instellingen voor netwerkstroomlogboeken lezen/schrijven/verwijderen. |
| Microsoft.Insights/LogDefinitions/Lezen |Deze toestemming is nodig voor gebruikers die via de portal toegang nodig hebben tot activiteitslogboeken. |
| Microsoft.Insights/Logprofiles/[Lezen, schrijven, verwijderen] |Logprofielen lezen/schrijven/verwijderen (activiteitslogboek streamen naar gebeurtenishub of opslagaccount). |
| Microsoft.Insights/MetricAlerts/[Lezen, schrijven, verwijderen] |Bijna realtime metrische waarschuwingen lezen/schrijven/verwijderen |
| Microsoft.Insights/MetricDefinitions/Read |Lees metrische definities (lijst met beschikbare metrische typen voor een resource). |
| Microsoft.Insights/Metrics/Read |Lees statistieken voor een resource. |
| Microsoft.Insights/Register/Actie |Registreer de Azure Monitor-bronprovider. |
| Microsoft.Insights/ScheduledQueryRules/[Lezen, schrijven, verwijderen] |Logboekwaarschuwingen lezen/schrijven/verwijderen in Azure Monitor. |



> [!NOTE]
> Toegang tot waarschuwingen, diagnostische instellingen en statistieken voor een resource vereist dat de gebruiker Leestoegang heeft tot het resourcetype en de reikwijdte van die bron. Het maken van ("schrijven") een diagnostische instelling of logboekprofiel dat archiveert naar een opslagaccount of streams naar gebeurtenishubs vereist dat de gebruiker ook met ListKeys toestemming heeft voor de doelbron.
> 
> 

Met de bovenstaande tabel u bijvoorbeeld een aangepaste RBAC-rol maken voor een 'Activity Log Reader':

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Beveiligingsoverwegingen voor het bewaken van gegevens
Het controleren van gegevens, met name logboekbestanden, kan gevoelige informatie bevatten, zoals IP-adressen of gebruikersnamen. Het bewaken van gegevens van Azure is er in drie basisvormen:

1. Het activiteitenlogboek, waarin alle besturingsvlakacties op uw Azure-abonnement worden beschreven.
2. resourcelogboeken, die logboeken worden uitgestoten door een resource.
3. Statistieken, die worden uitgestoten door resources.

Alle drie deze gegevenstypen kunnen worden opgeslagen in een opslagaccount of worden gestreamd naar Event Hub, die beide Azure-bronnen voor algemene doeleinden zijn. Omdat dit bronnen voor algemene doeleinden zijn, is het maken, verwijderen en openen ervan een bevoorrechte bewerking die is gereserveerd voor een beheerder. We raden u aan de volgende praktijken te gebruiken voor monitoringgerelateerde bronnen om misbruik te voorkomen:

* Gebruik één speciaal opslagaccount voor het bewaken van gegevens. Als u bewakingsgegevens moet scheiden in meerdere opslagaccounts, moet u nooit het gebruik van een opslagaccount delen tussen monitoring- en niet-bewakingsgegevens, omdat dit per ongeluk degenen die alleen toegang nodig hebben tot bewakingsgegevens (bijvoorbeeld een SIEM van derden) toegang tot niet-bewakingsgegevens.
* Gebruik één speciale servicebus of naamruimte voor gebeurtenishubs in alle diagnostische instellingen om dezelfde reden als hierboven.
* Beperk de toegang tot bewakingsgerelateerde opslagaccounts of gebeurtenishubs door ze in een afzonderlijke brongroep te houden en [gebruik het bereik](../../role-based-access-control/overview.md#scope) van uw bewakingsrollen om de toegang tot alleen die resourcegroep te beperken.
* Geef de ListKeys nooit toestemming voor opslagaccounts of gebeurtenishubs in het abonnementsbereik wanneer een gebruiker alleen toegang nodig heeft tot bewakingsgegevens. Geef deze machtigingen in plaats daarvan aan de gebruiker bij een resource- of resourcegroep (als u een speciale controlebrongroep hebt).

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Beperking van de toegang tot opslagaccounts met betrekking tot monitoring
Wanneer een gebruiker of toepassing toegang nodig heeft tot bewakingsgegevens in een opslagaccount, moet u [een Account SAS genereren](https://msdn.microsoft.com/library/azure/mt584140.aspx) op het opslagaccount dat bewakingsgegevens bevat met alleen-lezen toegang op serviceniveau tot blob-opslag. In PowerShell lijkt dit misschien op:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

U het token vervolgens aan de entiteit geven die moet worden gelezen vanuit dat opslagaccount, en het kan een lijst en lezen van alle blobs in dat opslagaccount.

Als u deze machtiging met RBAC moet beheren, u die entiteit ook de microsoft.storage/storageaccounts/lijstsleutels/actietoestemming verlenen voor dat specifieke opslagaccount. Dit is nodig voor gebruikers die een diagnostische instelling of logboekprofiel moeten kunnen instellen om te archiveren op een opslagaccount. U bijvoorbeeld de volgende aangepaste RBAC-rol maken voor een gebruiker of toepassing die slechts vanuit één opslagaccount hoeft te worden gelezen:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzRoleDefinition -Role $role 
```

> [!WARNING]
> Met de machtiging ListKeys kan de gebruiker de primaire en secundaire opslagaccountsleutels weergeven. Deze toetsen geven de gebruiker alle ondertekende machtigingen (lezen, schrijven, blobs maken, blobs verwijderen, enz.) voor alle ondertekende services (blob, wachtrij, tabel, bestand) in dat opslagaccount. We raden u aan om indien mogelijk een Hierboven beschreven Account SAS te gebruiken.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Toegang tot monitoringgerelateerde gebeurtenishubs beperken
Een vergelijkbaar patroon kan worden gevolgd met gebeurtenishubs, maar eerst moet u een speciale machtigingsregel voor luisteren maken. Als u toegang wilt verlenen tot een toepassing die alleen naar monitoringgerelateerde gebeurtenishubs hoeft te luisteren, gaat u als volgt te werk:

1. Maak een beleid voor gedeelde toegang op de gebeurtenishub(s) die zijn gemaakt voor het streamen van bewakingsgegevens met alleen Listen-claims. Dit kan in het portaal. U het bijvoorbeeld 'monitoringReadOnly' noemen. Indien mogelijk wilt u die sleutel rechtstreeks aan de consument geven en de volgende stap overslaan.
2. Als de consument de sleutel ad hoc moet kunnen krijgen, verleent u de gebruiker de actie ListKeys voor die gebeurtenishub. Dit is ook nodig voor gebruikers die een diagnostische instelling of logboekprofiel moeten kunnen instellen om naar gebeurtenishubs te streamen. U bijvoorbeeld een RBAC-regel maken:
   
   ```powershell
   $role = Get-AzRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>Bewaking binnen een beveiligd virtueel netwerk

Azure Monitor heeft toegang nodig tot uw Azure-bronnen om de services te leveren die u inschakelt. Als u uw Azure-bronnen wilt controleren terwijl u ze toch beveiligt van toegang tot het openbare internet, u de volgende instellingen inschakelen.

### <a name="secured-storage-accounts"></a>Beveiligde opslagaccounts 

Het controleren van gegevens wordt vaak naar een opslagaccount geschreven. U ervoor zorgen dat de gegevens die naar een opslagaccount worden gekopieerd, niet toegankelijk zijn voor onbevoegde gebruikers. Voor extra beveiliging u netwerktoegang vergrendelen om alleen uw geautoriseerde bronnen en vertrouwde Microsoft-services toegang te geven tot een opslagaccount door een opslagaccount te beperken tot het gebruik van 'geselecteerde netwerken'.
![Dialoogvenster](./media/roles-permissions-security/secured-storage-example.png) Azure-opslaginstellingen Azure-monitor wordt beschouwd als een van deze 'vertrouwde Microsoft-services' Als u vertrouwde Microsoft-services toegang geeft tot uw beveiligde opslag, heeft Azure-monitor toegang tot uw beveiligde opslagaccount; het schrijven van Azure Monitor-bronlogboeken, activiteitenlogboeken en statistieken in uw opslagaccount onder deze beveiligde voorwaarden. Hierdoor kan Log Analytics ook logboeken van beveiligde opslag lezen.   


Zie [Netwerkbeveiliging en Azure Storage](../../storage/common/storage-network-security.md) voor meer informatie

## <a name="next-steps"></a>Volgende stappen
* [Lees meer over RBAC en machtigingen in Resource Manager](../../role-based-access-control/overview.md)
* [Lees het overzicht van monitoring in Azure](../../azure-monitor/overview.md)


