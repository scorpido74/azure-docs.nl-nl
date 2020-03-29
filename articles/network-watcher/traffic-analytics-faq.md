---
title: Veelgestelde azure-verkeersanalyses | Microsoft Documenten
description: Krijg antwoorden op enkele van de meest gestelde vragen over verkeersanalyse.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: damendo
ms.openlocfilehash: 5e31ed905f05070c8715a63ef3386b0006df0a75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840618"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Verkeersanalyse veelgestelde vragen

Dit artikel verzamelt op één plaats veel van de meest gestelde vragen over verkeersanalyse in Azure Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Wat zijn de vereisten voor het gebruik van verkeersanalyse?

Traffic Analytics vereist de volgende voorwaarden:

- Een abonnement met Network Watcher ingeschakeld.
- Netwerkbeveiligingsgroep (NSG)-stroomlogboeken ingeschakeld voor de NSG's die u wilt controleren.
- Een Azure Storage-account om raw flow logs op te slaan.
- Een Azure Log Analytics-werkruimte, met lees- en schrijftoegang.

Uw account moet voldoen aan een van de volgende gegevens om verkeersanalyses in te schakelen:

- Uw account moet een van de volgende RBAC-rollen (Role Based Access Control) hebben: eigenaar, bijdrager, lezer of netwerkbijdrager.
- Als uw account niet is toegewezen aan een van de eerder vermelde rollen, moet het worden toegewezen aan een aangepaste rol waaraan de volgende acties is toegewezen, op abonnementsniveau.
            
    - Microsoft.Network/applicationGateways/lezen
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/lezen 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTabellen/lezen
    - Microsoft.Network/virtualNetworkGateways/lezen 
    - Microsoft.Network/virtualNetworks/lezen
        
Ga als een ander over een abonnement:

1. Meld u aan bij Azure met **Login-AzAccount**. 

2. Selecteer het vereiste abonnement met **Select-AzSubscription**. 

3. Als u alle rollen wilt weergeven die aan een bepaalde gebruiker zijn toegewezen, gebruikt u **Get-AzRoleAssignment -SignInName [gebruikerse-mail] -IncludeClassicAdministrators**. 

Als u geen uitvoer ziet, neemt u contact op met de desbetreffende abonnementsbeheerder om toegang te krijgen tot het uitvoeren van de opdrachten. Zie [Toegangsbeheer op basis van rollen beheren met Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)voor meer informatie.


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>In welke Azure-regio's is Traffic Analytics beschikbaar?

U verkeersanalyse voor NSG's gebruiken in een van de volgende ondersteunde regio's:
- Canada - midden
- VS - west-centraal
- VS - oost
- VS - oost 2
- VS - noord-centraal
- VS - zuid-centraal
- VS - centraal
- VS - west
- VS - west 2
- Frankrijk - centraal
- Europa -west
- Europa - noord
- Brazilië - zuid
- Verenigd Koninkrijk West
- Verenigd Koninkrijk Zuid
- Australië - oost
- Australië - zuidoost 
- Azië - oost
- Azië - zuidoost
- Korea - centraal
- India - centraal
- India - zuid
- Japan - oost
- Japan - west
- VS (overheid) - Virginia
- China Oost 2

De werkruimte Log Analytics moet in de volgende regio's bestaan:
- Canada - midden
- VS - west-centraal
- VS - oost
- VS - oost 2
- VS - noord-centraal
- VS - zuid-centraal
- VS - centraal
- VS - west
- VS - west 2
- Frankrijk - centraal
- Europa -west
- Europa - noord
- Verenigd Koninkrijk West
- Verenigd Koninkrijk Zuid
- Australië - oost
- Australië - zuidoost
- Azië - oost
- Azië - zuidoost 
- Korea - centraal
- India - centraal
- Japan - oost
- VS (overheid) - Virginia
- China Oost 2

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Kunnen de NSG's die ik inschakel, stroomlogboeken inschakelen voor in verschillende regio's dan mijn werkruimte?

Ja, deze NSG's kunnen zich in verschillende regio's bevinden dan uw Log Analytics-werkruimte.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Kunnen meerdere NSG's binnen één werkruimte worden geconfigureerd?

Ja.

## <a name="can-i-use-an-existing-workspace"></a>Kan ik een bestaande werkruimte gebruiken?

Ja. Als u een bestaande werkruimte selecteert, controleert u of deze is gemigreerd naar de nieuwe querytaal. Als u de werkruimte niet wilt upgraden, moet u een nieuwe werkruimte maken. Zie [Azure Monitor-logboeken upgraden naar nieuwe logboeken.](../log-analytics/log-analytics-log-search-upgrade.md)

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Kan mijn Azure Storage-account één abonnement hebben en heeft mijn Log Analytics-werkruimte een ander abonnement?

Ja, uw Azure Storage-account kan in één abonnement zijn en uw Log Analytics-werkruimte kan een ander abonnement hebben.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Kan ik raw logs opslaan in een ander abonnement?

Nee. U ruwe logboeken opslaan in elk opslagaccount waar een NSG is ingeschakeld voor stroomlogboeken. Zowel het opslagaccount als de raw-logboeken moeten echter in hetzelfde abonnement en dezelfde regio zijn.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Wat moet ik doen als ik een NSG voor verkeersanalyse niet kan configureren vanwege een fout 'Niet gevonden'?

Selecteer een ondersteund gebied. Als u een niet-ondersteund gebied selecteert, ontvangt u een foutmelding 'Niet gevonden'. De ondersteunde regio's worden eerder in dit artikel vermeld.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Wat moet ik doen als ik de status 'Niet laden' krijg onder de pagina NSG-stroomlogboeken?

De Microsoft.Insights-provider moet zijn geregistreerd om te kunnen werken voor stroomregistratie. Als u niet zeker weet of de Microsoft.Insights-provider is geregistreerd voor uw abonnement, vervangt u *xxxxx-xxxxx-xxxxxx-xxxx* in de volgende opdracht en voert u de volgende opdrachten uit van PowerShell:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Ik heb de oplossing geconfigureerd. Waarom zie ik niets op het dashboard?

Het kan tot 30 minuten duren voordat het dashboard de eerste keer wordt weergegeven. De oplossing moet eerst voldoende gegevens verzamelen om zinvolle inzichten te verkrijgen. Dan genereert het rapporten. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Wat als ik dit bericht krijg: "We konden geen gegevens vinden in deze werkruimte voor een geselecteerd tijdsinterval. Probeer het tijdsinterval te wijzigen of selecteer een andere werkruimte."?

Probeer de volgende opties:
- Wijzig het tijdsinterval in de bovenste balk.
- Selecteer een andere Log Analytics-werkruimte in de bovenste balk.
- Probeer na 30 minuten toegang te krijgen tot verkeersanalyses als deze onlangs is ingeschakeld.
    
Als er problemen blijven bestaan, u zich zorgen maken in het [gebruikersstemforum.](https://feedback.azure.com/forums/217313-networking?category_id=195844)

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Wat als ik dit bericht: "Het analyseren van uw NSG stroom logs voor de eerste keer. Dit proces kan 20-30 minuten in beslag nemen. Kom na verloop van tijd terug. 2) Als de bovenstaande stap niet werkt en uw werkruimte onder de gratis SKU valt, controleer dan hier uw werkruimtegebruik om over quota te valideren, anders verwijzen we u naar veelgestelde vragen voor meer informatie."?

Mogelijk ziet u dit bericht omdat:
- Traffic Analytics is onlangs ingeschakeld en heeft mogelijk nog niet genoeg gegevens verzameld om zinvolle inzichten te verkrijgen.
- U gebruikt de gratis versie van de werkruimte Log Analytics en deze heeft de quotumlimieten overschreden. Mogelijk moet u een werkruimte met een grotere capaciteit gebruiken.
    
Als er problemen blijven bestaan, u zich zorgen maken in het [gebruikersstemforum.](https://feedback.azure.com/forums/217313-networking?category_id=195844)
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Wat als ik dit bericht krijg: "Het lijkt erop dat we resources data (Topology) en geen flows informatie hebben. Klik ondertussen hier om bronnengegevens te bekijken en te verwijzen naar veelgestelde vragen voor meer informatie."?

U ziet de bronneninformatie op het dashboard; er zijn echter geen stroomgerelateerde statistieken aanwezig. Gegevens zijn mogelijk niet aanwezig omdat er geen communicatiestromen tussen de resources zijn. Wacht 60 minuten en controleer de status opnieuw. Als het probleem blijft bestaan en u zeker weet dat er communicatiestromen tussen resources bestaan, u zorgen uiten in het [voice-forum gebruiker.](https://feedback.azure.com/forums/217313-networking?category_id=195844)

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Kan ik verkeersanalyses configureren met PowerShell of een Azure Resource Manager-sjabloon of client?

U verkeersanalyses configureren met Windows PowerShell vanaf versie 6.2.1. Zie Set-AzNetworkWatcherConfigFlowLog voor het configureren van stroomlogboekregistratie en verkeersanalyse voor een specifieke NSG met behulp van de cmdlet [Set-AzNetworkWatcherConfigFlowLog](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkwatcherconfigflowlog). Zie [Get-AzNetworkWatcherFlowLogStatus](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkwatcherflowlogstatus)voor een specifieke NSG voor de status van stroomregistratie en verkeersanalyse.

Momenteel u geen Azure Resource Manager-sjabloon gebruiken om verkeersanalyses te configureren.

Zie de volgende voorbeelden om verkeersanalyses te configureren met behulp van een Azure Resource Manager-client.

**Stel een cmdlet-voorbeeld in:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Download cmdlet voorbeeld:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```


## <a name="how-is-traffic-analytics-priced"></a>Hoe wordt Traffic Analytics geprijsd?

Traffic Analytics wordt gemeten. De meting is gebaseerd op de verwerking van stroomlogboekgegevens door de service en het opslaan van de resulterende verbeterde logboeken in een Log Analytics-werkruimte. 

Bijvoorbeeld, volgens het [prijsplan](https://azure.microsoft.com/pricing/details/network-watcher/), rekening houdend met West Central US regio, als flow logs gegevens die zijn opgeslagen in een opslagaccount verwerkt door Traffic Analytics is 10 GB en verbeterde logs ingenomen in Log Analytics werkruimte is 1 GB dan de toepasselijke kosten zijn: 10 x 2,3 $ + 1 x 2,76 $ = 25,76 $

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Hoe vaak verwerkt Traffic Analytics gegevens?

Raadpleeg de [sectie gegevensaggregatie](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema#data-aggregation) in traffic analytics-schema en gegevensaggregatiedocument

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Hoe besluit Traffic Analytics dat een IP kwaadaardig is? 

Traffic Analytics vertrouwt op interne threat intelligence-systemen van Microsoft om een IP als kwaadaardig te beschouwen. Deze systemen maken gebruik van diverse telemetriebronnen zoals Microsoft-producten en -services, de Microsoft Digital Crimes Unit (DCU), het Microsoft Security Response Center (MSRC) en externe feeds en bouwen er veel informatie op. Sommige van deze gegevens zijn Microsoft Internal. Als een bekend IP wordt gemarkeerd als kwaadaardig, hef dan een ondersteuningsticket om de details te weten.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Hoe kan ik waarschuwingen instellen voor Traffic Analytics-gegevens?

Traffic Analytics heeft geen ingebouwde ondersteuning voor waarschuwingen. Aangezien Traffic Analytics-gegevens echter worden opgeslagen in Log Analytics, u aangepaste query's schrijven en er waarschuwingen op instellen. Stappen:
- U de shortlink voor Log Analytics gebruiken in Traffic Analytics. 
- Gebruik het [hier gedocumenteerde schema](traffic-analytics-schema.md) om uw query's te schrijven 
- Klik op 'Nieuwe waarschuwingsregel' om de waarschuwing te maken
- Documentatie met [logboekwaarschuwingen verwijzen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) om de waarschuwing te maken

## <a name="how-do-i-check-which-vms-are-receiving-most-on-premise-traffic"></a>Hoe controleer ik welke VM's het meeste on-premise verkeer ontvangen

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            | where <Scoping condition>
            | mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
            | where isnotempty(vm) 
             | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
            | make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by vm
            | render timechart

  Voor IP's:

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
            | where isnotempty(IP) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
            | make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by IP
            | render timechart

Gebruik voor tijd het formaat : yyyy-mm-dd 00:00:00

## <a name="how-do-i-check-standard-deviation-in-traffic-recieved-by-my-vms-from-on-premise-machines"></a>Hoe controleer ik de standaarddeviatie in het verkeer dat mijn VM's ontvangen van on-premise machines

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
            | where isnotempty(vm) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
            | summarize deviation = stdev(traffic)  by vm


Voor IP's:

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and FlowType_s == "S2S" 
            //| where <Scoping condition>
            | mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
            | where isnotempty(IP) 
            | extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
            | summarize deviation = stdev(traffic)  by IP
            
## <a name="how-do-i-check-which-ports-are-reachable-or-bocked-between-ip-pairs-with-nsg-rules"></a>Hoe controleer ik welke poorten bereikbaar zijn (of bocked) tussen IP-paren met NSG-regels

            AzureNetworkAnalytics_CL
            | where SubType_s == "FlowLog" and TimeGenerated between (startTime .. endTime)
            | extend sourceIPs = iif(isempty(SrcIP_s), split(SrcPublicIPs_s, " ") , pack_array(SrcIP_s)),
            destIPs = iif(isempty(DestIP_s), split(DestPublicIPs_s," ") , pack_array(DestIP_s))
            | mvexpand SourceIp = sourceIPs to typeof(string)
            | mvexpand DestIp = destIPs to typeof(string)
            | project SourceIp = tostring(split(SourceIp, "|")[0]), DestIp = tostring(split(DestIp, "|")[0]), NSGList_s, NSGRule_s, DestPort_d, L4Protocol_s, FlowStatus_s 
            | summarize DestPorts= makeset(DestPort_d) by SourceIp, DestIp, NSGList_s, NSGRule_s, L4Protocol_s, FlowStatus_s

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Hoe kan ik navigeren met het toetsenbord in de geokaartweergave?

De geokaartpagina bevat twee hoofdsecties:
    
- **Banner**: De banner boven aan de geokaart biedt knoppen om verkeersdistributiefilters te selecteren (bijvoorbeeld Implementatie, Verkeer uit landen/regio's en Kwaadaardig). Wanneer u een knop selecteert, wordt het betreffende filter op de kaart toegepast. Als u bijvoorbeeld de knop Actief selecteert, markeert de kaart de actieve datacenters in uw implementatie.
- **Kaart:** Onder de banner toont de kaartsectie de verkeersverdeling tussen Azure-datacenters en -landen/-regio's.
    
### <a name="keyboard-navigation-on-the-banner"></a>Toetsenbordnavigatie op de banner
    
- Standaard is de selectie op de geokaartpagina voor de banner het filter 'Azure DC's'.
- Als u naar een ander `Tab` filter `Right arrow` wilt gaan, gebruikt u de sleutel of de sleutel. Gebruik de `Shift+Tab` `Left arrow` toets om achteruit te gaan. Voorwaartse navigatie is van links naar rechts, gevolgd door van boven naar beneden.
- Druk `Enter` op `Down` of op de pijltoets om het geselecteerde filter toe te passen. Op basis van filterselectie en -implementatie worden één of meerdere knooppunten onder de kaartsectie gemarkeerd.
- Als u wilt schakelen `Ctrl+F6`tussen banner en kaart, drukt u op .
        
### <a name="keyboard-navigation-on-the-map"></a>Toetsenbordnavigatie op de kaart
    
- Nadat u een filter op de `Ctrl+F6`banner hebt geselecteerd en ingedrukt, wordt de focus verplaatst naar een van de gemarkeerde knooppunten **(Azure-datacenter** of **Land/Regio)** in de kaartweergave.
- Als u naar andere gemarkeerde knooppunten `Tab` in `Right arrow` de kaart wilt gaan, gebruikt u een van beide of de sleutel voor voorwaartse beweging. Gebruik `Shift+Tab` of `Left arrow` de sleutel voor achterwaartse beweging.
- Als u een gemarkeerd knooppunt in `Enter` de `Down arrow` kaart wilt selecteren, gebruikt u de oftoets.
- Bij de selectie van dergelijke knooppunten wordt de focus verplaatst naar het **informatiegereedschapsvak** voor het knooppunt. Standaard wordt de focus verplaatst naar de gesloten knop in het **informatiegereedschapsvak**. Gebruik `Right arrow` en `Left arrow` toetsen om respectievelijk vooruit en achteruit te gaan om verder te gaan in de **boxweergave.** Het `Enter` indrukken heeft hetzelfde effect als het selecteren van de gefocuste knop in het **informatiegereedschapsvak.**
- Wanneer u `Tab` drukt terwijl de focus ligt op het **informatiegereedschapsvak,** gaat de focus naar de eindpunten in hetzelfde continent als het geselecteerde knooppunt. Gebruik `Right arrow` de `Left arrow` toetsen en toetsen om door deze eindpunten te gaan.
- Als u naar andere stroomeindpunten `Tab` of continentclusters `Shift+Tab` wilt gaan, gebruikt u voor voorwaartse beweging en voor achterwaartse beweging.
- Wanneer de focus ligt op **continentclusters,** gebruikt u de `Enter` of `Down` pijltoetsen om de eindpunten in het continentcluster te markeren. Als u door eindpunten en de knop sluiten in het `Right arrow` informatievak van het continentcluster wilt gaan, gebruikt u de oftoets `Left arrow` voor respectievelijk voorwaartse en achterwaartse beweging. Op elk eindpunt `Shift+L` u overschakelen naar de verbindingslijn van het geselecteerde knooppunt naar het eindpunt. U kunt `Shift+L` opnieuw drukken om naar het geselecteerde eindpunt te gaan.
        
### <a name="keyboard-navigation-at-any-stage"></a>Toetsenbordnavigatie in elk stadium
    
- `Esc`hiermee wordt de uitgebreide selectie samengevouwen.
- De `Up arrow` toets voert dezelfde `Esc`actie uit als . De `Down arrow` toets voert dezelfde `Enter`actie uit als .
- Met `Shift+Plus` de functie `Shift+Minus` om in te zoomen en uit te zoomen.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Hoe kan ik navigeren met het toetsenbord in de virtuele netwerktopologieweergave?

De topologiepagina voor virtuele netwerken bevat twee hoofdsecties:
    
- **Banner**: De banner boven aan de topologie van virtuele netwerken biedt knoppen voor het selecteren van verkeersdistributiefilters (bijvoorbeeld verbonden virtuele netwerken, losgekoppelde virtuele netwerken en openbare IP's). Wanneer u een knop selecteert, wordt het desbetreffende filter toegepast op de topologie. Als u bijvoorbeeld de knop Actief selecteert, markeert de topologie de actieve virtuele netwerken in uw implementatie.
- **Topologie**: Onder de banner toont de sectie topologie de verkeersverdeling tussen virtuele netwerken.
    
### <a name="keyboard-navigation-on-the-banner"></a>Toetsenbordnavigatie op de banner
    
- Standaard is de selectie op de topologiepagina van virtuele netwerken voor de banner het filter 'Verbonden VNets'.
- Als u naar een `Tab` ander filter wilt gaan, gebruikt u de sleutel om verder te gaan. Gebruik de `Shift+Tab` sleutel om achteruit te gaan. Voorwaartse navigatie is van links naar rechts, gevolgd door van boven naar beneden.
- Druk `Enter` om het geselecteerde filter toe te passen. Op basis van de filterselectie en -implementatie worden één of meerdere knooppunten (virtueel netwerk) onder de sectie topologie gemarkeerd.
- Als u wilt schakelen tussen de `Ctrl+F6`banner en de topologie, drukt u op .
        
### <a name="keyboard-navigation-on-the-topology"></a>Toetsenbordnavigatie op de topologie
    
- Nadat u een filter op de `Ctrl+F6`banner hebt geselecteerd en hebt ingedrukt, wordt de focus verplaatst naar een van de gemarkeerde knooppunten **(VNet)** in de topologieweergave.
- Als u naar andere gemarkeerde knooppunten in `Shift+Right arrow` de topologieweergave wilt gaan, gebruikt u de sleutel voor voorwaartse beweging. 
- Bij gemarkeerde knooppunten wordt de focus verplaatst naar het **informatiegereedschapsvak** voor het knooppunt. Standaard wordt de focus verplaatst naar de knop **Meer details** in het **vak Informatiegereedschap**. Als u verder wilt bewegen `Right arrow` in `Left arrow` de **boxweergave,** gebruikt u de toetsen en toetsen om respectievelijk vooruit en achteruit te bewegen. Het `Enter` indrukken heeft hetzelfde effect als het selecteren van de gefocuste knop in het **informatiegereedschapsvak.**
- Bij de selectie van dergelijke knooppunten u alle verbindingen één `Shift+Left arrow` voor één bezoeken door op de toets te drukken. De focus wordt verplaatst naar het **informatiegereedschapsvak** van die verbinding. Op elk moment kan de focus worden verschoven `Shift+Right arrow` naar het knooppunt door opnieuw te drukken.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Hoe kan ik navigeren met het toetsenbord in de subnettopologieweergave?

De pagina over de topologie van virtuele subnetwerken bevat twee hoofdsecties:
    
- **Banner**: De banner boven aan de virtuele subnetwerken topologie biedt knoppen om verkeersdistributiefilters te selecteren (bijvoorbeeld Active, Medium en Gateway subnetten). Wanneer u een knop selecteert, wordt het desbetreffende filter toegepast op de topologie. Als u bijvoorbeeld de knop Actief selecteert, markeert de topologie het actieve virtuele subnetwerk in uw implementatie.
- **Topologie**: Onder de banner toont de sectie topologie de verkeersverdeling tussen virtuele subnetwerken.
    
### <a name="keyboard-navigation-on-the-banner"></a>Toetsenbordnavigatie op de banner
    
- Standaard is de selectie op de topologiepagina van virtuele subnetwerken voor de banner het filter Subnetten.
- Als u naar een `Tab` ander filter wilt gaan, gebruikt u de sleutel om verder te gaan. Gebruik de `Shift+Tab` sleutel om achteruit te gaan. Voorwaartse navigatie is van links naar rechts, gevolgd door van boven naar beneden.
- Druk `Enter` om het geselecteerde filter toe te passen. Op basis van filterselectie en -implementatie worden één of meerdere knooppunten (subnet) onder de sectie topologie gemarkeerd.
- Als u wilt schakelen tussen de `Ctrl+F6`banner en de topologie, drukt u op .
        
### <a name="keyboard-navigation-on-the-topology"></a>Toetsenbordnavigatie op de topologie
    
- Nadat u een filter op de `Ctrl+F6`banner hebt geselecteerd en hebt ingedrukt, wordt de focus verplaatst naar een van de gemarkeerde knooppunten **(Subnet)** in de topologieweergave.
- Als u naar andere gemarkeerde knooppunten in `Shift+Right arrow` de topologieweergave wilt gaan, gebruikt u de sleutel voor voorwaartse beweging. 
- Bij gemarkeerde knooppunten wordt de focus verplaatst naar het **informatiegereedschapsvak** voor het knooppunt. Standaard wordt de focus verplaatst naar de knop **Meer details** in het **vak Informatiegereedschap**. Gebruik `Right arrow` en `Left arrow` toetsen om respectievelijk vooruit en achteruit te gaan om verder te gaan in de **boxweergave.** Het `Enter` indrukken heeft hetzelfde effect als het selecteren van de gefocuste knop in het **informatiegereedschapsvak.**
- Bij de selectie van dergelijke knooppunten u alle verbindingen `Shift+Left arrow` één voor één bezoeken door op de toets te drukken. De focus wordt verplaatst naar het **informatiegereedschapsvak** van die verbinding. Op elk moment kan de focus worden verschoven `Shift+Right arrow` naar het knooppunt door opnieuw te drukken.    

