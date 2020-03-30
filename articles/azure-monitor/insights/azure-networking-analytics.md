---
title: Azure Networking Analytics-oplossing in Azure Monitor | Microsoft Documenten
description: U de Azure Networking Analytics-oplossing in Azure Monitor gebruiken om azure-netwerkbeveiligingsgroeplogboeken en Azure Application Gateway-logboeken te controleren.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2018
ms.openlocfilehash: 1045f86db5e1a9ed1979a266937974045e401e27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275566"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Azure-oplossingen voor netwerkbewaking in Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor biedt de volgende oplossingen voor het bewaken van uw netwerken:
* Network Performance Monitor (NPM) naar
    * De status van uw netwerk bewaken
* Azure Application Gateway-analyses om te bekijken
    * Azure Application Gateway-logboeken
    * Azure Application Gateway-statistieken
* Oplossingen voor het monitoren en auditeren van netwerkactiviteiten op uw cloudnetwerk
    * [Traffic Analytics](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
    * Azure Network Security Group Analytics

## <a name="network-performance-monitor-npm"></a>Netwerkprestatiemonitor (NPM)

De [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) management oplossing is een netwerkmonitoringoplossing, die de status, beschikbaarheid en bereikbaarheid van netwerken bewaakt.  Het wordt gebruikt om de connectiviteit tussen:

* Public cloud en on-premises
* Datacenters en gebruikerslocaties (filialen)
* Subnetten die verschillende lagen van een toepassing met meerdere lagen hosten.

Zie [Netwerkprestatiemonitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview)voor meer informatie .

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Azure Application Gateway en Network Security Group analytics
Ga als het gaat om het gebruik van de oplossingen:
1. Voeg de beheeroplossing toe aan Azure Monitor en
2. Stel diagnostische gegevens in om de diagnose naar een Log Analytics-werkruimte in Azure Monitor te leiden. Het is niet nodig om de logboeken naar Azure Blob-opslag te schrijven.

U diagnostische gegevens en de bijbehorende oplossing inschakelen voor één of beide toepassingsgateway- en netwerkbeveiligingsgroepen.

Als u diagnostische bronlogboekregistratie voor een bepaald resourcetype niet inschakelt, maar de oplossing installeert, zijn de dashboardbladen voor die bron leeg en wordt een foutbericht weergegeven.

> [!NOTE]
> In januari 2017 is de ondersteunde manier om logboeken van Application Gateways en Network Security Groups naar een log analytics-werkruimte te verzenden gewijzigd. Als u de **Azure Networking Analytics-oplossing (afgeschaft) ziet,** raadpleegt u [de migratie van de oude Networking Analytics-oplossing](#migrating-from-the-old-networking-analytics-solution) voor stappen die u moet volgen.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Details van azure-netwerkgegevensverzameling controleren
De azure application gateway-analyses en de analysebeheeroplossingen van de Network Security Group verzamelen diagnostische logboeken rechtstreeks van Azure Application Gateways en Network Security Groups. Het is niet nodig om de logboeken naar Azure Blob-opslag te schrijven en er is geen agent vereist voor het verzamelen van gegevens.

In de volgende tabel worden methoden voor het verzamelen van gegevens en andere details weergegeven over de manier waarop gegevens worden verzameld voor Azure Application Gateway-analyses en de analyse van de netwerkbeveiligingsgroep.

| Platform | Direct agent | Systems Center Operations Manager-agent | Azure | Operations Manager vereist? | Operations Manager agent gegevens verzonden via management groep | Verzamelingsfrequentie |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |wanneer aangemeld |


## <a name="azure-application-gateway-analytics-solution-in-azure-monitor"></a>Azure Application Gateway-analyseoplossing in Azure Monitor

![Azure Application Gateway Analytics-symbool](media/azure-networking-analytics/azure-analytics-symbol.png)

De volgende logboeken worden ondersteund voor Application Gateways:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

De volgende statistieken worden ondersteund voor Application Gateways:again


* 5 minuten doorvoer

### <a name="install-and-configure-the-solution"></a>De oplossing installeren en configureren
Gebruik de volgende instructies om de Azure Application Gateway-analyseoplossing te installeren en configureren:

1. Schakel de Azure Application Gateway analytics-oplossing in vanuit [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) of met behulp van het proces dat is beschreven in [Azure Monitor-oplossingen toevoegen vanuit de Galerie Oplossingen.](../../azure-monitor/insights/solutions.md)
2. Schakel diagnostische logboekregistratie in voor de [toepassingsgateways](../../application-gateway/application-gateway-diagnostics.md) die u wilt controleren.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Azure Application Gateway-diagnose inschakelen in de portal

1. Navigeer in de Azure-portal naar de toepassingsgatewaybron die u wilt bewaken.
2. Selecteer *Diagnostische logboeken* om de volgende pagina te openen.

   ![afbeelding van Azure Application Gateway-bron](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics01.png)
3. Klik *op Diagnostische gegevens inschakelen* om de volgende pagina te openen.

   ![afbeelding van Azure Application Gateway-bron](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics02.png)
4. Als u diagnostische gegevens wilt inschakelen, klikt u *onder* *Status*.
5. Klik op het selectievakje *Verzenden naar logboekanalyse*.
6. Selecteer een bestaande Log Analytics-werkruimte of maak een werkruimte.
7. Klik op het selectievakje onder **Logboek** voor elk van de logboektypen die u wilt verzamelen.
8. Klik *op Opslaan* om de logboekregistratie van diagnostische gegevens in te schakelen naar Azure Monitor.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Azure-netwerkdiagnose inschakelen met PowerShell

Het volgende PowerShell-script geeft een voorbeeld van hoe u bronlogboekregistratie voor toepassingsgateways inschakelen.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Azure Application Gateway-analyse gebruiken
![afbeelding van de tegel Azure Application Gateway-analyse](media/azure-networking-analytics/log-analytics-appgateway-tile.png)

Nadat u op de tegel **Azure Application Gateway-analyses** in het overzicht hebt geklikt, u samenvattingen van uw logboeken weergeven en vervolgens inzoomen op details voor de volgende categorieën:

* Logboeken voor toepassingsgatewaytoegang
  * Client- en serverfouten voor toepassingsgatewaytoegangslogboeken
  * Aanvragen per uur voor elke application gateway
  * Mislukte aanvragen per uur voor elke toepassingsgateway
  * Fouten per gebruikersagent voor toepassingsgateways
* Prestaties van toepassingsgateway
  * Hoststatus voor application gateway
  * Maximaal en 95e percentiel voor mislukte aanvragen voor toepassinggateway

![afbeelding van azure Application Gateway analytics-dashboard](media/azure-networking-analytics/log-analytics-appgateway01.png)

![afbeelding van azure Application Gateway analytics-dashboard](media/azure-networking-analytics/log-analytics-appgateway02.png)

Bekijk op het azure **application gateway analytics-dashboard** de overzichtsgegevens in een van de bladen en klik vervolgens op een pagina om gedetailleerde informatie op de zoekpagina van het logboek weer te geven.

Op een van de zoekpagina's van het logboek u resultaten bekijken op tijd, gedetailleerde resultaten en uw logboekzoekgeschiedenis. U ook filteren op facetten om de resultaten te beperken.


## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Azure Network Security Group analytics-oplossing in Azure Monitor

![Azure Network Security Group Analytics-symbool](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> De analyseoplossing voor netwerkbeveiliginggroep wordt verplaatst naar community-ondersteuning omdat de functionaliteit is vervangen door [Traffic Analytics.](../../network-watcher/traffic-analytics.md)
> - De oplossing is nu beschikbaar in [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) en is binnenkort niet meer beschikbaar in de Azure Marketplace.
> - Voor bestaande klanten die de oplossing al aan hun werkruimte hebben toegevoegd, blijft deze zonder wijzigingen functioneren.
> - Microsoft blijft ondersteuning bieden voor het verzenden van NSG-bronlogboeken naar uw werkruimte met behulp van diagnostische instellingen.

De volgende logboeken worden ondersteund voor netwerkbeveiligingsgroepen:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>De oplossing installeren en configureren
Gebruik de volgende instructies om de Azure Networking Analytics-oplossing te installeren en te configureren:

1. Schakel de Azure Network Security Group analytics-oplossing in vanuit [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) of met behulp van het proces dat is beschreven in [Azure Monitor-oplossingen toevoegen vanuit de Galerie Oplossingen.](../../azure-monitor/insights/solutions.md)
2. Schakel diagnostische logboekregistratie in voor de bronnen van de [netwerkbeveiligingsgroep](../../virtual-network/virtual-network-nsg-manage-log.md) die u wilt controleren.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Diagnose van Azure-netwerkbeveiligingsgroep inschakelen in de portal

1. Navigeer in de Azure-portal naar de bron netwerkbeveiligingsgroep om te controleren
2. Diagnostische *logboeken selecteren* om de volgende pagina te openen

   ![afbeelding van azure Network Security Group-bron](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. Klik *op Diagnostische gegevens inschakelen* om de volgende pagina te openen

   ![afbeelding van azure Network Security Group-bron](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Als u diagnostische gegevens wilt inschakelen, klikt u *op Onder* *Status*
5. Klik op het selectievakje *Verzenden naar logboekanalyse*
6. Selecteer een bestaande Log Analytics-werkruimte of maak een werkruimte
7. Klik op het selectievakje onder **Logboek** voor elk van de logboektypen die u wilt verzamelen
8. Klik *op Opslaan* om de logboekregistratie van diagnostische gegevens in te schakelen in Logboekanalyse

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Azure-netwerkdiagnose inschakelen met PowerShell

Het volgende PowerShell-script geeft een voorbeeld van hoe u bronlogboekregistratie inschakelt voor netwerkbeveiligingsgroepen
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Analyse van Azure Network Security Group gebruiken
Nadat u op de **analysetegel Azure Network Security Group** in het overzicht hebt geklikt, u samenvattingen van uw logboeken bekijken en vervolgens inzoomen op details voor de volgende categorieën:

* Geblokkeerde stromen van netwerkbeveiligingsgroep
  * Regels voor netwerkbeveiliging met geblokkeerde stromen
  * MAC-adressen met geblokkeerde stromen
* Netwerkbeveiligingsgroep toegestane stromen
  * Regels voor netwerkbeveiliging met toegestane stromen
  * MAC-adressen met toegestane stromen

![afbeelding van het azure Network Security Group analytics-dashboard](media/azure-networking-analytics/log-analytics-nsg01.png)

![afbeelding van het azure Network Security Group analytics-dashboard](media/azure-networking-analytics/log-analytics-nsg02.png)

Bekijk in het azure **Network Security Group analytics-dashboard** de overzichtsgegevens in een van de bladen en klik vervolgens op een van de pagina om gedetailleerde informatie op de zoekpagina van het logboek weer te geven.

Op een van de zoekpagina's van het logboek u resultaten bekijken op tijd, gedetailleerde resultaten en uw logboekzoekgeschiedenis. U ook filteren op facetten om de resultaten te beperken.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migreren vanuit de oude Networking Analytics-oplossing
In januari 2017 is de ondersteunde manier om logboeken van Azure Application Gateways en Azure Network Security Groups naar een log Analytics-werkruimte te verzenden, gewijzigd. Deze wijzigingen bieden de volgende voordelen:
+ Logboeken worden rechtstreeks naar Azure Monitor geschreven zonder dat u een opslagaccount hoeft te gebruiken
+ Minder latentie vanaf het moment dat logboeken worden gegenereerd in Azure Monitor
+ Minder configuratiestappen
+ Een algemene indeling voor alle typen Azure-diagnostiek

Ga als beste de bijgewerkte oplossingen gebruiken:

1. [Diagnostische gegevens configureren om rechtstreeks naar Azure Monitor te worden verzonden vanuit Azure Application Gateways](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Diagnostische gegevens configureren om rechtstreeks naar Azure Monitor te worden verzonden vanuit Azure Network Security Groups](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. De *Azure Application Gateway Analytics* en de *Azure Network Security Group Analytics-oplossing* inschakelen met behulp van het proces dat is beschreven in [Azure Monitor-oplossingen toevoegen vanuit de Galerie Oplossingen](solutions.md)
3. Opgeslagen query's, dashboards of waarschuwingen bijwerken om het nieuwe gegevenstype te gebruiken
   + Type is naar AzureDiagnostics. U de ResourceType gebruiken om te filteren op Azure-netwerklogboeken.

     | In plaats van: | Gebruiken: |
     | --- | --- |
     | NetworkApplicationgateways &#124; waar OperationName=="ApplicationGatewayAccess" | AzureDiagnostics &#124; waar ResourceType=="APPLICATIONGATEWAYS" en OperationName=="ApplicationGatewayAccess" |
     | NetworkApplicationgateways &#124; waar OperationName=="ApplicationGatewayPerformance" | AzureDiagnostics &#124; waar ResourceType=="APPLICATIONGATEWAYS" en OperationName=="ApplicationGatewayPerformance" |
     | NetworkSecuritygroepen | AzureDiagnostics &#124; waar ResourceType=="NETWORKSECURITYGROUPS" |

   + Voor elk veld met een \_achtervoegsel van s, \_d of \_g in de naam, wijzigt u het eerste teken in kleine letters
   + Voor elk veld met een \_achtervoegsel van o in naam worden de gegevens opgesplitst in afzonderlijke velden op basis van de geneste veldnamen.
4. Verwijder de *Azure Networking Analytics-oplossing (Deprecated)*
   + Als u PowerShell gebruikt, gebruikt u`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Gegevens die vóór de wijziging zijn verzameld, zijn niet zichtbaar in de nieuwe oplossing. U deze gegevens blijven opvragen met de oude namen Type en veld.

## <a name="troubleshooting"></a>Problemen oplossen
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Volgende stappen
* Gebruik [Logboekquery's in Azure Monitor](../log-query/log-query-overview.md) om gedetailleerde Azure-diagnostische gegevens weer te geven.
