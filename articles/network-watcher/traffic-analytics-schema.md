---
title: Azure-verkeersanalyseschema | Microsoft Documenten
description: Begrijp het schema van Traffic Analytics om azure-netwerkbeveiligingsgroepstroomlogboeken te analyseren.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: ccfbb92c27e4508595f19c2ea6900730cde609b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74666372"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Schema en gegevensaggregatie in Traffic Analytics

Traffic Analytics is een cloudgebaseerde oplossing die inzicht biedt in de activiteit van gebruikers en toepassingen in cloudnetwerken. Traffic Analytics analyseert NSG-stroomlogboeken (Network Watcher Network Security Group) om inzicht te geven in de verkeersstroom in uw Azure-cloud. Met verkeersanalyse u:

- Visualiseer netwerkactiviteiten voor uw Azure-abonnementen en identificeer hotspots.
- Identificeer beveiligingsbedreigingen voor en beveilig uw netwerk met informatie zoals open poorten, toepassingen die toegang tot internet proberen en virtuele machines (VM) die verbinding maken met malafide netwerken.
- Inzicht in verkeersstroompatronen in Azure-regio's en internet om uw netwerkimplementatie te optimaliseren voor prestaties en capaciteit.
- Ontlok foutieve netwerkconfiguraties die leiden tot mislukte verbindingen in uw netwerk.
- Weet netwerkgebruik in bytes, pakketten of stromen.

### <a name="data-aggregation"></a>Gegevensaggregatie

1. Alle stroomlogboeken op een NSG tussen 'FlowIntervalStartTime_t' en 'FlowIntervalEndTime_t' worden met intervallen van één minuut in het opslagaccount vastgelegd als blobs voordat ze worden verwerkt door Traffic Analytics.
2. Het standaardverwerkingsinterval van Traffic Analytics is 60 minuten. Dit betekent dat Traffic Analytics elke 60 minuten blobs uit opslag kiest voor aggregatie. Als het gekozen verwerkingsinterval 10 minuten is, kiest Traffic Analytics na elke 10 minuten blobs uit het opslagaccount.
3. Stromen met hetzelfde bron-IP-ip, doel-IP, doelpoort, NSG-naam, NSG-regel, Stroomrichting en Transportlaagprotocol (TCP of UDP) (Opmerking: bronpoort is uitgesloten voor aggregatie) worden door Traffic Analytics in één stroom geknuppeld
4. Deze enkele record is ingericht (Details in de sectie hieronder) en opgenomen in Log Analytics door Traffic Analytics.Dit proces kan maximaal 1 uur duren.
5. FlowStartTime_t veld geeft het eerste optreden van een dergelijke geaggregeerde stroom (dezelfde vier-tuple) in de flow log processing interval tussen "FlowIntervalStartTime_t" en "FlowIntervalEndTime_t".
6. Voor elke resource in TA zijn de stromen die in de gebruikersinterface zijn aangegeven, de totale stromen die door de NSG worden gezien, maar in Log Analytics ziet de gebruiker alleen de afzonderlijke, beperkte record. Als u alle stromen wilt zien, gebruikt u het veld blob_id, waarnaar kan worden verwezen vanuit Storage. Het totale aantal stroom voor die record komt overeen met de afzonderlijke stromen die in de blob worden weergegeven.

De onderstaande query helpt u alle stroomlogboeken van on-premises in de afgelopen 30 dagen te bekijken.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
Als u het blobpad voor de stromen in de bovengenoemde query wilt weergeven, gebruikt u de onderstaande query:

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

De bovenstaande query construeert een URL om rechtstreeks toegang te krijgen tot de blob. De URL met plaatshouders vindt u hieronder:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Velden die worden gebruikt in het Traffic Analytics-schema
  > [!IMPORTANT]
  > Het Traffic Analytics Schema is bijgewerkt op 22 augustus 2019. Het nieuwe schema biedt bron- en doel-IP's die afzonderlijk de noodzaak verwijderen om het veld FlowDirection te ontleden, waardoor query's eenvoudiger worden. </br>
  > FASchemaVersion_s bijgewerkt van 1 tot 2. </br>
  > Afgeschafte velden: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s, FlowCount_d </br>
  > Nieuwe velden: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > Afgeschafte velden zijn beschikbaar tot 22 november 2019.

Traffic Analytics is gebouwd bovenop Log Analytics, zodat u aangepaste query's uitvoeren op gegevens die zijn ingericht door Traffic Analytics en waarschuwingen op dezelfde manier instellen.

Hieronder staan de velden in het schema en wat ze betekenen

| Veld | Indeling | Opmerkingen |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabel voor gegevens over Verkeersanalyse
| SubType_s | Stroomlog | Subtype voor de stroomlogboeken. Gebruik alleen "FlowLog", andere waarden van SubType_s zijn voor interne werking van het product |
| FASchemaVersion_s |   2   | Schemaversie. Komt niet overeen met de NSG Flow Log-versie |
| TimeProcessed_t   | Datum en tijd in UTC  | Het tijdstip waarop de Traffic Analytics de raw flow logs van het opslagaccount heeft verwerkt |
| FlowIntervalStartTime_t | Datum en tijd in UTC |  Begintijd van het verwerkingsinterval voor stroomlogboeken. Dit is de tijd van waaruit het stroominterval wordt gemeten |
| FlowIntervalEndTime_t | Datum en tijd in UTC | Eindtijd van het verwerkingsinterval van het stroomlogboek |
| FlowStartTime_t | Datum en tijd in UTC |  Eerste gebeurtenis van de stroom (die wordt geaggregeerd) in de flow log processing interval tussen "FlowIntervalStartTime_t" en "FlowIntervalEndTime_t". Deze stroom wordt geaggregeerd op basis van aggregatielogica |
| FlowEndTime_t | Datum en tijd in UTC | Laatste gebeurtenis van de stroom (die wordt geaggregeerd) in de flow log processing interval tussen "FlowIntervalStartTime_t" en "FlowIntervalEndTime_t". In termen van stroomlogboek v2 bevat dit veld de tijd waarop de laatste stroom met dezelfde vier-tuple is gestart (gemarkeerd als 'B' in de raw flow record) |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * Extern publiek <br> * MaliciousFlow <br> * Onbekend Privé <br> * Onbekend | Definitie in notities onder de tabel |
| SrcIP_s | IP-adres van bron | Wordt leeg in het geval van AzurePublic- en ExternalPublic-stromen |
| DestIP_s | IP-adres van doel | Wordt leeg in het geval van AzurePublic- en ExternalPublic-stromen |
| VMIP_s | IP van de VM | Wordt gebruikt voor AzurePublic- en ExternalPublic-stromen |
| PublicIP_s | Openbare IP-adressen | Wordt gebruikt voor AzurePublic- en ExternalPublic-stromen |
| DestPort_d | Doelpoort | Haven waar het verkeer binnenkomt |
| L4Protocol_s  | * T <br> * U  | Transportprotocol. T = TCP <br> U = UDP |
| L7Protocol_s  | Protocolnaam | Afgeleid van de bestemmingshaven |
| FlowDirection_s | * I = Binnenkomend<br> * O = Uitgaand | Richting van de stroom in/uit NSG per stroomlogboek |
| FlowStatus_s  | * A = Toegestaan door NSG-regel <br> * D = Geweigerd door NSG-regel  | Status van de stroom toegestaan/ngeblokkeerd door NSG volgens stroomlogboek |
| NSGList_s | \<>>\/<\/ RESOURCEGROUP_NAME>><>>NSG_NAME> | Network Security Group (NSG) gekoppeld aan de stroom |
| NSGRules_s | \<Indexwaarde \| \<0)>\| \<NSG_RULENAME>\| \<stroomrichting \| \<>stroomstatus>Van Regel> |  NSG-regel die deze stroom heeft toegestaan of geweigerd |
| NSGRule_s | NSG_RULENAME |  NSG-regel die deze stroom heeft toegestaan of geweigerd |
| NSGRuleType_s | * Gebruiker gedefinieerd * Standaard |   Het type NSG-regel dat door de stroom wordt gebruikt |
| MACAddress_s | MAC-adres | MAC-adres van de NIC waar de stroom werd vastgelegd |
| Subscription_s | Het abonnement op de virtuele netwerk-/netwerkinterface/virtuele machine van Azure wordt in dit veld ingevuld | Alleen van toepassing op FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow en UnknownPrivate flowtypes (stroomtypen waarbij slechts één kant azure is) |
| Subscription1_s | Abonnements-id | Abonnements-ID van virtuele netwerk/ netwerkinterface/ virtuele machine waartoe de bron-IP in de stroom behoort |
| Subscription2_s | Abonnements-id | Abonnements-ID van virtuele netwerk/ netwerkinterface/ virtuele machine waartoe de bestemming-IP in de stroom behoort |
| Region_s | Azure-regio van virtuele netwerk/ netwerkinterface/ virtuele machine waartoe het IP-adres in de stroom behoort | Alleen van toepassing op FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow en UnknownPrivate flowtypes (stroomtypen waarbij slechts één kant azure is) |
| Region1_s | Azure-regio | Azure-regio van virtuele netwerk/ netwerkinterface/ virtuele machine waartoe de bron-IP in de stroom behoort |
| Region2_s | Azure-regio | Azure-regio van virtueel netwerk waartoe het doel-IP in de stroom behoort |
| NIC_s | \<resourcegroup_Name \/ \<>NetworkInterfaceName> |  NIC die is gekoppeld aan het verzenden of ontvangen van het verkeer van de vm |
| NIC1_s | <resourcegroup_Name\<>/ NetworkInterfaceName> | NIC die is gekoppeld aan het bron-IP in de stroom |
| NIC2_s | <resourcegroup_Name\<>/ NetworkInterfaceName> | NIC die is gekoppeld aan het doel-IP in de stroom |
| VM_s | <resourcegroup_Name \/ \<>>networkinterfacenaam> | Virtuele machine die is gekoppeld aan de netwerkinterface NIC_s |
| VM1_s | <resourcegroup_Name\<>/ VirtualMachineName> | Virtuele machine die is gekoppeld aan het bron-IP in de stroom |
| VM2_s | <resourcegroup_Name\<>/ VirtualMachineName> | Virtuele machine die is gekoppeld aan het doel-IP in de stroom |
| Subnet_s | <ResourceGroup_Name>/<\<VNET_Name>/ SubnetName> | Subnet gekoppeld aan de NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<\<VNET_Name>/ SubnetName> | Subnet dat is gekoppeld aan het bron-IP in de stroom |
| Subnet2_s | <ResourceGroup_Name>/<\<VNET_Name>/ SubnetName>    | Subnet dat is gekoppeld aan het doel-IP in de stroom |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName\<>/ ApplicationGatewayName> | Toepassingsgateway die is gekoppeld aan het bron-IP in de stroom |
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName\<>/ ApplicationGatewayName> | Toepassingsgateway die is gekoppeld aan het doel-ip in de stroom |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName\<>/ LoadBalancerName> | Load balancer gekoppeld aan het bron-IP in de stroom |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName\<>/ LoadBalancerName> | Load balancer gekoppeld aan het doel-IP in de stroom |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName\<>/ LocalNetworkGatewayName> | Lokale netwerkgateway die is gekoppeld aan het bron-IP in de stroom |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName\<>/ LocalNetworkGatewayName> | Lokale netwerkgateway die is gekoppeld aan het doel-IP in de stroom |
| ConnectionType_s | Mogelijke waarden zijn VNetPeering, VpnGateway en ExpressRoute |    Verbindingstype |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName\<>/ ConnectionName> | Verbindingsnaam. Voor flowtype P2S wordt dit <gateway name>opgemaakt als _<VPN Client IP> |
| ConnectingVNets_s | Lijst met virtuele netwerknamen met spatie gescheiden | In het geval van hub en spoke topologie worden virtuele hubnetwerken hier bevolkt |
| Country_s | Landcode met twee letters (ISO 3166-1 alfa-2) | Ingevuld voor stroomtype ExternOpenbaar. Alle IP-adressen in PublicIPs_s veld delen dezelfde landcode |
| AzureRegion_s | Azure-regiolocaties | Gevuld voor stroomtype AzurePublic. Alle IP-adressen in PublicIPs_s veld delen de Azure-regio |
| AllowedInFlows_d | | Aantal binnenkomende stromen die zijn toegestaan. Dit geeft het aantal stromen weer dat dezelfde vier-tuple-instroom heeft gedeeld met de netwerkinterface waarop de stroom is vastgelegd |
| DeniedInFlows_d |  | Aantal binnenkomende stromen die zijn geweigerd. (Binnenkomend naar de netwerkinterface waarbij de stroom is vastgelegd) |
| AllowedOutFlows_d | | Aantal uitgaande stromen die zijn toegestaan (Uitgaand naar de netwerkinterface waarop de stroom is vastgelegd) |
| DeniedOutFlows_d  | | Aantal uitgaande stromen die zijn geweigerd (Uitgaand naar de netwerkinterface waarop de stroom is vastgelegd) |
| FlowCount_d | Afgeschaft. Totale stromen die overeenkomen met dezelfde vier-tuple. In het geval van stroomtypen ExternalPublic en AzurePublic omvat het aantal ook de stromen van verschillende PublicIP-adressen.
| InboundPackets_d | Pakketten die zijn ontvangen zoals vastgelegd op de netwerkinterface waar nsg-regel werd toegepast | Dit wordt alleen ingevuld voor het schema van versie 2 van NSG-stroomlogboek |
| OutboundPackets_d  | Pakketten verzonden zoals vastgelegd op de netwerkinterface waar NSG-regel werd toegepast | Dit wordt alleen ingevuld voor het schema van versie 2 van NSG-stroomlogboek |
| InboundBytes_d |  Bytes ontvangen zoals vastgelegd op de netwerkinterface waar NSG-regel werd toegepast | Dit wordt alleen ingevuld voor het schema van versie 2 van NSG-stroomlogboek |
| OutboundBytes_d | Bytes verzonden zoals vastgelegd op de netwerkinterface waar nsg-regel werd toegepast | Dit wordt alleen ingevuld voor het schema van versie 2 van NSG-stroomlogboek |
| CompletedFlows_d  |  | Dit wordt alleen gevuld met niet-nulwaarde voor het schema van versie 2 van NSG-stroomlogboek |
| PublicIPs_s | <PUBLIC_IP \| \<>\| \<>\| \<>\| \<>\| \<>\| \<OUTBOUND_BYTES INBOUND_PACKETS>>FLOW_ENDED_COUNT FLOW_STARTED_COUNT>>>>OUTBOUND_PACKETS>INBOUND_BYTES>>>. | Vermeldingen gescheiden door balken |
| SrcPublicIPs_s | <SOURCE_PUBLIC_IP \| \<FLOW_STARTED_COUNT \| \<>\| \<FLOW_STARTED_COUNT \| \<FLOW_STARTED_COUNT \| \<>\| \<FLOW_ENDED_COUNT INBOUND_BYTES>OUTBOUND_BYTES>INBOUND_PACKETS>OUTBOUND_PACKETS>FLOW_ENDED_COUNT FLOW_ENDED_COUNT FLOW_ENDED_COUNT>>>>. | Vermeldingen gescheiden door balken |
| DestPublicIPs_s | <DESTINATION_PUBLIC_IP \| \<>\| \<FLOW_STARTED_COUNT \| \<>\| \<>\| \<>\| \<>>>>OUTBOUND_BYTES INBOUND_PACKETS>OUTBOUND_PACKETS FLOW_ENDED_COUNT>>INBOUND_BYTES INBOUND_BYTES>>>>>>. | Vermeldingen gescheiden door balken |

### <a name="notes"></a>Opmerkingen

1. In het geval van AzurePublic- en ExternalPublic-stromen wordt het Azure VM-IP van de klant ingevuld in VMIP_s veld, terwijl de openbare IP-adressen worden ingevuld in het veld PublicIPs_s. Voor deze twee stroomtypen moeten we VMIP_s en PublicIPs_s gebruiken in plaats van SrcIP_s en DestIP_s velden. Voor AzurePublic- en ExternalPublicIP-adressen worden we verder samengevoegd, zodat het aantal records dat is ingenomen in de werkruimte voor klantlogboekanalyse minimaal is. (Dit veld wordt binnenkort afgeschaft en we moeten SrcIP_ en DestIP_s gebruiken, afhankelijk van of azure VM de bron of de bestemming in de stroom was)
1. Details voor stroomtypen: Op basis van de IP-adressen die betrokken zijn bij de stroom, categoriseren we de stromen in de volgende stroomtypen:
1. IntraVNet : beide IP-adressen in de stroom bevinden zich in hetzelfde Azure Virtual Network.
1. InterVNet - IP-adressen in de stroom bevinden zich in de twee verschillende Azure Virtual Networks.
1. S2S – (Site To Site) Een van de IP-adressen behoort tot Azure Virtual Network, terwijl het andere IP-adres behoort tot het klantennetwerk (Site) dat via vpn-gateway of Express Route is verbonden met het Azure Virtual Network.
1. P2S - (Point To Site) Een van de IP-adressen behoort tot Azure Virtual Network, terwijl het andere IP-adres behoort tot het klantennetwerk (Site) dat via de VPN-gateway is verbonden met het Azure Virtual Network.
1. AzurePublic - Een van de IP-adressen behoort tot Azure Virtual Network, terwijl het andere IP-adres behoort tot Azure Internal Public IP-adressen die eigendom zijn van Microsoft. Openbare IP-adressen die eigendom zijn van de klant, maken geen deel uit van dit stroomtype. Bijvoorbeeld, elke klant eigendom VM verzenden van verkeer naar een Azure Service (Storage endpoint) zou worden gecategoriseerd onder dit stroomtype.
1. Extern Openbaar - Een van de IP-adressen behoort tot Azure Virtual Network, terwijl het andere IP-adres een openbaar IP-adres is dat niet in Azure is, wordt niet gerapporteerd als kwaadaardig in de ASC-feeds die Traffic Analytics verbruikt voor het verwerkingsinterval tussen " FlowIntervalStartTime_t" en "FlowIntervalEndTime_t".
1. MaliciousFlow - Een van de IP-adressen behoren tot het virtuele Azure-netwerk, terwijl het andere IP-adres een openbaar IP-adres is dat niet in Azure is en als kwaadaardig wordt gerapporteerd in de ASC-feeds die Traffic Analytics verbruikt voor het verwerkingsinterval tussen " FlowIntervalStartTime_t" en "FlowIntervalEndTime_t".
1. UnknownPrivate - Een van de IP-adressen behoort tot Azure Virtual Network, terwijl het andere IP-adres behoort tot een privé-IP-bereik zoals gedefinieerd in RFC 1918 en niet door Traffic Analytics kan worden toegewezen aan een site die eigendom is van een klant of Azure Virtual Network.
1. Onbekend : kan het ip-adres in de stromen niet in kaart brengen met de klanttopologie in Azure en on-premises (site).
1. Sommige veldnamen worden \_toegevoegd \_met s of d. Deze betekenen GEEN bron en bestemming, maar geven respectievelijk de gegevenstypen tekenreeks en decimale aan.

### <a name="next-steps"></a>Volgende stappen
Zie [Veelgestelde vragen](traffic-analytics-faq.md) over verkeersanalyse Voor informatie over functionaliteit raadpleegt [U de documentatie voor verkeersanalyses](traffic-analytics.md)
