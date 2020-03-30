---
title: Azure-verkeersanalyse | Microsoft Documenten
description: Meer informatie over het analyseren van Azure-netwerkbeveiligingsgroepstroomlogboeken met verkeersanalyses.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: damendo
ms.reviewer: vinigam
ms.openlocfilehash: 83164a615cacc067e5f1ea6a1dd6ce0f0fd9d540
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298853"
---
# <a name="traffic-analytics"></a>Traffic Analytics

Traffic Analytics is een cloudgebaseerde oplossing die inzicht biedt in de activiteit van gebruikers en toepassingen in cloudnetwerken. Verkeersanalyse analyseert NSG-stroomlogboeken (Network Watcher Network Security Group) om inzicht te geven in de verkeersstroom in uw Azure-cloud. Met verkeersanalyse u:

- Visualiseer netwerkactiviteiten voor uw Azure-abonnementen en identificeer hotspots.
- Identificeer beveiligingsbedreigingen voor en beveilig uw netwerk met informatie zoals open poorten, toepassingen die toegang tot internet proberen en virtuele machines (VM) die verbinding maken met malafide netwerken.
- Inzicht in verkeersstroompatronen in Azure-regio's en internet om uw netwerkimplementatie te optimaliseren voor prestaties en capaciteit.
- Ontlok foutieve netwerkconfiguraties die leiden tot mislukte verbindingen in uw netwerk.

> [!NOTE]
> Traffic Analytics ondersteunt nu het verzamelen van NSG Flow Logs-gegevens op een hogere frequentie van 10 minuten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Waarom traffic analytics?

Het is essentieel om uw eigen netwerk te bewaken, te beheren en te kennen voor compromisloze beveiliging, naleving en prestaties. Het kennen van uw eigen omgeving is van het grootste belang om deze te beschermen en te optimaliseren. U moet vaak de huidige status van het netwerk kennen, wie verbinding maakt, waar ze verbinding maken, welke poorten open staan voor het internet, verwacht netwerkgedrag, onregelmatig netwerkgedrag en plotselinge toename van het verkeer.

Cloudnetwerken zijn anders dan on-premises bedrijfsnetwerken, waar u routers en switches met een netstroom of gelijkwaardig protocol hebt, die de mogelijkheid bieden om IP-netwerkverkeer te verzamelen wanneer het een netwerkinterface binnenkomt of verlaat. Door verkeersstroomgegevens te analyseren, u een analyse maken van de netwerkverkeersstroom en het volume.

Azure virtuele netwerken hebben NSG-stroomlogboeken, die u informatie geven over het binnendringen en uitstromen van IP-verkeer via een netwerkbeveiligingsgroep die is gekoppeld aan afzonderlijke netwerkinterfaces, VM's of subnetten. Door ruwe NSG-stroomlogboeken te analyseren en intelligentie van beveiliging, topologie en geografie in te voegen, kan verkeersanalyse u inzicht geven in de verkeersstroom in uw omgeving. Traffic Analytics biedt informatie zoals de meeste communicerende hosts, de meeste communicerende toepassingsprotocollen, de meeste gesprekken over hostparen, toegestaan/geblokkeerd verkeer, inkomend/uitgaand verkeer, open internetpoorten, de meeste blokkeringsregels, verkeersdistributie per Azure-datacenter, virtueel netwerk, subnetten of malafide netwerken.

## <a name="key-components"></a>Belangrijkste onderdelen

- **Netwerkbeveiligingsgroep (NSG):** bevat een lijst met beveiligingsregels die netwerkverkeer toestaan of weigeren voor resources die zijn verbonden met een Azure Virtual Network. NSG's kunnen worden gekoppeld aan subnetten, afzonderlijke virtuele machines (klassiek) of afzonderlijke netwerkinterfaces (NIC) die zijn gekoppeld aan VM’s (Resource Manager). Zie [Overzicht van de netwerkbeveiligingsgroep](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)voor meer informatie.
- **Netwerkbeveiligingsgroep (NSG)-stroomlogboeken**: Hiermee u informatie over binnendringen en uitgangen IP-verkeer bekijken via een netwerkbeveiligingsgroep. NSG-stroomlogboeken worden geschreven in json-indeling en tonen uitgaande en binnenkomende stromen per regel, de NIC waarop de stroom van toepassing is, vijf-tuple-informatie over de stroom (bron/bestemming IP-adres, bron/doelpoort en protocol) en als het verkeer is toegestaan of geweigerd. Zie [NSG-stroomlogboeken](network-watcher-nsg-flow-logging-overview.md)voor meer informatie over NSG-stroomlogboeken.
- **Log Analytics:** een Azure-service die bewakingsgegevens verzamelt en de gegevens opslaat in een centrale opslagplaats. Deze gegevens kunnen gebeurtenissen, prestatiegegevens of aangepaste gegevens bevatten die via de Azure API worden geleverd. Na verzameling zijn de gegevens beschikbaar voor waarschuwingen, analyse en export. Monitoringtoepassingen zoals netwerkprestatiemonitor en verkeersanalyse worden gebouwd met Azure Monitor-logboeken als basis. Zie [Azure Monitor-logboeken voor](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)meer informatie .
- **Log Analytics-werkruimte**: een instantie van Azure Monitor-logboeken, waarbij de gegevens met betrekking tot een Azure-account worden opgeslagen. Zie [Een werkruimte voor Logboekanalyse maken](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)voor meer informatie over logboekanalysewerkruimten.
- **Network Watcher:** een regionale service waarmee u de omstandigheden op netwerkscenarioniveau in Azure controleren en diagnosticeren. U NSG-stroomlogboeken in- en uitschakelen met Network Watcher. Zie [Network Watcher](network-watcher-monitoring-overview.md)voor meer informatie.

## <a name="how-traffic-analytics-works"></a>Hoe verkeersanalyse werkt

Verkeersanalyse onderzoekt de ruwe NSG-stroomlogboeken en legt minder logboeken vast door veelvoorkomende stromen te aggregeren tussen hetzelfde bron-IP-adres, het IP-adres van de bestemming, de doelpoort en het protocol. Host 1 (IP-adres: 10.10.10.10) communiceren met host 2 (IP-adres: 10.10.20.10), 100 keer over een periode van 1 uur met poort (bijvoorbeeld 80) en protocol (bijvoorbeeld http). Het beperkte logboek heeft één vermelding, dat Host 1 & Host 2 100 keer communiceerde over een periode van 1 uur met behulp van poort *80* en protocol *HTTP,* in plaats van 100 vermeldingen. Beperkte logboeken worden uitgebreid met gegevens over geografie, beveiliging en topologie en vervolgens opgeslagen in een werkruimte log Analytics. De volgende afbeelding toont de gegevensstroom:

![Gegevensstroom voor verwerking NSG-stroomlogboeken](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions-nsg"></a>Ondersteunde regio's: NSG 

U verkeersanalyse voor NSG's gebruiken in een van de volgende ondersteunde regio's:

* Canada - midden
* VS - west-centraal
* VS - oost
* VS - oost 2
* VS - noord-centraal
* VS - zuid-centraal
* VS - centraal
* VS - west
* VS - west 2
* Frankrijk - centraal
* Europa -west
* Europa - noord
* Brazilië - zuid
* Verenigd Koninkrijk West
* Verenigd Koninkrijk Zuid
* Australië - oost
* Australië - zuidoost
* Azië - oost
* Azië - zuidoost
* Korea - centraal
* India - centraal
* India - zuid
* Japan - oost 
* Japan - west
* VS (overheid) - Virginia
* China Oost 2

## <a name="supported-regions-log-analytics-workspaces"></a>Ondersteunde regio's: Logboekanalysewerkruimten

De werkruimte Log Analytics moet in de volgende regio's bestaan:
* Canada - midden
* VS - west-centraal
* VS - oost
* VS - oost 2
* VS - noord-centraal
* VS - zuid-centraal
* VS - centraal
* VS - west
* VS - west 2
* VS - centraal
* Frankrijk - centraal
* Europa -west
* Europa - noord
* Brazilië - zuid
* Verenigd Koninkrijk West
* Verenigd Koninkrijk Zuid
* Australië - oost
* Australië - zuidoost
* Azië - oost
* Azië - zuidoost
* Korea - centraal
* India - centraal
* Japan - oost
* VS (overheid) - Virginia
* China Oost 2

## <a name="prerequisites"></a>Vereisten

### <a name="user-access-requirements"></a>Vereisten voor gebruikerstoegang

Uw account moet lid zijn van een van de volgende [ingebouwde Azure-rollen:](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)

|Implementatiemodel   | Rol                   |
|---------          |---------               |
|Resource Manager   | Eigenaar                  |
|                   | Inzender            |
|                   | Lezer                 |
|                   | Inzender voor netwerken    |

Als uw account niet is toegewezen aan een van de ingebouwde rollen, moet het worden toegewezen aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) waaraan de volgende acties is toegewezen, op abonnementsniveau:

- "Microsoft.Network/applicationGateways/read"
- "Microsoft.Network/connections/read"
- "Microsoft.Network/loadBalancers/read"
- "Microsoft.Network/localNetworkGateways/read"
- "Microsoft.Network/networkInterfaces/read"
- "Microsoft.Network/networkSecurityGroups/read"
- "Microsoft.Network/publicIPAddresses/read"
- "Microsoft.Network/routeTabellen/lezen"
- "Microsoft.Network/virtualNetworkGateways/read"
- "Microsoft.Network/virtualNetworks/read"
- "Microsoft.Network/expressRouteCircuits/read"

Zie [Veelgestelde vragen](traffic-analytics-faq.md)over verkeersanalyses voor informatie over het controleren van gebruikerstoegangsmachtigingen.

### <a name="enable-network-watcher"></a>Network Watcher inschakelen

Om verkeer te analyseren, moet u een bestaande netwerkwatcher hebben of [een netwerkwatcher](network-watcher-create.md) in elke regio inschakelen waarvoor u NSG's hebt waarvoor u verkeer wilt analyseren. Verkeersanalyses kunnen worden ingeschakeld voor NSG's die worden gehost in een van de [ondersteunde regio's.](#supported-regions-nsg)

### <a name="select-a-network-security-group"></a>Een netwerkbeveiligingsgroep selecteren

Voordat u NSG-stroomlogboekregistratie inschakelt, moet u een netwerkbeveiligingsgroep hebben om stromen voor te registreren. Zie [Een netwerkbeveiligingsgroep maken](../virtual-network/manage-network-security-group.md#create-a-network-security-group) om er een te maken als u geen netwerkbeveiligingsgroep hebt.

Ga in Azure-portal naar **Netwerkwatcher**en selecteer **vervolgens NSG-stroomlogboeken**. Selecteer de netwerkbeveiligingsgroep waarvoor u een NSG-stroomlogboek wilt inschakelen, zoals in de volgende afbeelding wordt weergegeven:

![Selectie van NSG's die het mogelijk maken van NSG-stroomlogboek vereisen](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Als u verkeersanalyses probeert in te schakelen voor een NSG die wordt gehost in een andere regio dan de [ondersteunde regio's,](#supported-regions-nsg)ontvangt u een foutmelding 'Niet gevonden'.

## <a name="enable-flow-log-settings"></a>Instellingen voor stroomlogboeken inschakelen

Voordat u de instellingen voor stroomlogboeken inschakelt, moet u de volgende taken uitvoeren:

Registreer de Azure Insights-provider als deze nog niet is geregistreerd voor uw abonnement:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

Als u nog geen Azure Storage-account hebt om NSG-stroomlogboeken op te slaan, moet u een opslagaccount maken. U een opslagaccount maken met de opdracht die volgt. Voordat u de `<replace-with-your-unique-storage-account-name>` opdracht uitvoert, vervangt u een naam die uniek is voor alle Azure-locaties, tussen 3-24 tekens in lengte, met alleen getallen en kleine letters. U de naam van de resourcegroep ook wijzigen, indien nodig.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Selecteer de volgende opties, zoals in de afbeelding wordt weergegeven:

1. Selecteren *Voor* **status**
2. Selecteer *versie 2* voor de versie van **Stroomlogboeken**. Versie 2 bevat stroomsessiestatistieken (bytes en pakketten)
3. Selecteer een bestaand opslagaccount om de stroomlogboeken op te slaan. Als u de gegevens voor altijd wilt opslaan, stelt u de waarde in op *0*. U brengt azure-opslagkosten in rekening voor het opslagaccount. Zorg ervoor dat uw opslag niet is ingesteld op true.
4. **Stel Retentie** in op het aantal dagen waarvoor u gegevens wilt opslaan.
5. Selecteer *Aan* voor **traffic analytics-status**.
6. Selecteer verwerkingsinterval. Op basis van uw keuze worden stroomlogboeken verzameld van het opslagaccount en verwerkt door Traffic Analytics. U kiezen voor verwerkingsinterval van elke 1 uur of elke 10 minuten. 
7. Selecteer een bestaande Oms-werkruimte (Log Analytics) of selecteer **Nieuwe werkruimte maken** om een nieuwe werkruimte te maken. Een Log Analytics-werkruimte wordt door Traffic Analytics gebruikt om de geaggregeerde en geïndexeerde gegevens op te slaan die vervolgens worden gebruikt om de analyses te genereren. Als u een bestaande werkruimte selecteert, moet deze in een van de [ondersteunde regio's](#supported-regions-log-analytics-workspaces) bestaan en zijn deze geüpgraded naar de nieuwe querytaal. Als u een bestaande werkruimte niet wilt upgraden of geen werkruimte in een ondersteund gebied hebt, maakt u een nieuwe werkruimte. Zie [Azure Log Analytics-upgrade naar nieuwe logboekzoekopdrachten voor](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)meer informatie over querytalen.

> [!NOTE]
>De log analytics workspace die de traffic analytics-oplossing host en de NSG's hoeven zich niet in dezelfde regio te bevinden. U bijvoorbeeld verkeersanalyses hebben in een werkruimte in de regio West-Europa, terwijl u mogelijk NSG's in Oost-VS en West-VS hebt. Meerdere NSG's kunnen in dezelfde werkruimte worden geconfigureerd.

8. Selecteer **Opslaan**.

    ![Selectie van opslagaccount, Log Analytics-werkruimte en Traffic Analytics-enablement](./media/traffic-analytics/ta-customprocessinginterval.png)

Herhaal de vorige stappen voor alle andere NSG's waarvoor u verkeersanalyses wilt inschakelen. Gegevens uit stroomlogboeken worden naar de werkruimte verzonden, dus zorg ervoor dat de lokale wet- en regelgeving in uw land gegevensopslag toestaat in de regio waar de werkruimte bestaat. Als u verschillende verwerkingsintervallen voor verschillende NSG's hebt ingesteld, worden gegevens met verschillende intervallen verzameld. Bijvoorbeeld: U ervoor kiezen om verwerkingsinterval van 10 minuten in te schakelen voor kritieke VNET's en 1 uur voor niet-kritische VNETs.

U ook verkeersanalyses configureren met de [Cmdlet Set-AzNetworkConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) PowerShell in Azure PowerShell. Voer `Get-Module -ListAvailable Az` uit om de geïnstalleerde versie te vinden. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps).

## <a name="view-traffic-analytics"></a>Verkeersanalyses weergeven

Als u Traffic Analytics wilt bekijken, zoekt u **naar Network Watcher** in de zoekbalk van de portal. Selecteer **Traffic Analytics** in het linkermenu om traffic analytics en de mogelijkheden ervan te verkennen. 

![Toegang tot het Traffic Analytics-dashboard](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Het kan tot 30 minuten duren voordat het dashboard de eerste keer wordt weergegeven, omdat Traffic Analytics eerst voldoende gegevens moet verzamelen om zinvolle inzichten te verkrijgen, voordat het rapporten kan genereren.

## <a name="usage-scenarios"></a>Gebruiksscenario's

Enkele van de inzichten die u zou willen krijgen nadat Traffic Analytics volledig is geconfigureerd, zijn als volgt:

### <a name="find-traffic-hotspots"></a>Verkeershotspots zoeken

**Zoeken**

- Welke hosts, subnetten en virtuele netwerken verzenden of ontvangen het meeste verkeer, doorkruisen maximaal kwaadaardig verkeer en blokkeren belangrijke stromen?
    - Controleer vergelijkende grafiek voor host, subnet en virtueel netwerk. Als u begrijpt welke hosts, subnetten en virtuele netwerken het meeste verkeer verzenden of ontvangen, u de hosts identificeren die het meeste verkeer verwerken en of de verkeersverdeling correct wordt uitgevoerd.
    - U beoordelen of het verkeersvolume geschikt is voor een host. Is de omvang van het verkeer normaal gedrag, of is het verdienen verder onderzoek?
- Hoeveel inkomend/uitgaand verkeer is er?
    -   Wordt verwacht dat de host meer binnenkomend verkeer ontvangt dan uitgaand, of vice versa?
- Statistieken van geblokkeerd verkeer.
    - Waarom blokkeert een host een aanzienlijke hoeveelheid goedaardig verkeer? Dit gedrag vereist verder onderzoek en waarschijnlijk optimalisatie van de configuratie
- Statistieken van kwaadwillende toegestaan / geblokkeerd verkeer
  - Waarom ontvangt een host kwaadaardig verkeer en waarom stroomt uit kwaadaardige bron is toegestaan? Dit gedrag vereist verder onderzoek en waarschijnlijk optimalisatie van de configuratie.

    Selecteer **Alles weergeven**onder **Host,** zoals in de volgende afbeelding wordt weergegeven:

    ![Dashboard met host met de meeste verkeersdetails](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- De volgende afbeelding toont tijdtrending voor de top vijf pratende hosts en de stroomgerelateerde details (toegestaan - inkomende / uitgaande en geweigerde - inkomende / uitgaande stromen) voor een host:

    ![Top vijf meest pratende host trend](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Zoeken**

- Wat zijn de meest sprekende gastparen?
    - Verwacht gedrag zoals front-end of back-end communicatie of onregelmatig gedrag, zoals back-end internetverkeer.
- Statistieken van toegestaan/geblokkeerd verkeer
    - Waarom een host een aanzienlijk verkeersvolume toestaat of blokkeert
- Meest gebruikte toepassingsprotocol onder de meeste gespreksvierde hostparen:
    - Zijn deze toepassingen toegestaan op dit netwerk?
    - Zijn de toepassingen goed geconfigureerd? Gebruiken ze het juiste protocol voor communicatie? Selecteer **Alles weergeven** onder **Frequent gesprek,** zoals weergegeven in de volgende afbeelding:

        ![Dashboard met meest voorkomende conversatie](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- In de volgende afbeelding ziet u de trending van de tijd voor de top vijf gesprekken en de stroomgerelateerde details, zoals toegestane en geweigerde inkomende en uitgaande stromen voor een gesprekspaar:

    ![Top vijf spraakzame gespreksdetails en trend](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Zoeken**

- Welk toepassingsprotocol wordt het meest gebruikt in uw omgeving en welke probleemparen gebruiken het toepassingsprotocol het meest?
    - Zijn deze toepassingen toegestaan op dit netwerk?
    - Zijn de toepassingen goed geconfigureerd? Gebruiken ze het juiste protocol voor communicatie? Verwacht gedrag is gemeenschappelijke poorten zoals 80 en 443. Voor standaardcommunicatie vereisen deze mogelijk een configuratiewijziging als er ongebruikelijke poorten worden weergegeven. Selecteer **Alles weergeven** onder **Toepassingspoort**, in de volgende afbeelding:

        ![Dashboard met toptoepassingsprotocollen](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- De volgende foto's tonen tijdtrending voor de top vijf L7-protocollen en de stroomgerelateerde details (bijvoorbeeld toegestane en geweigerde stromen) voor een L7-protocol:

    ![Top vijf laag 7 protocollen details en trend](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Stroomgegevens voor toepassingsprotocol in logboekzoekopdrachten](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Zoeken**

- Capaciteitsbenuttingtrends van een VPN-gateway in uw omgeving.
    - Elke VPN SKU maakt een bepaalde hoeveelheid bandbreedte mogelijk. Worden de VPN-gateways onderbenut?
    - Bereiken uw gateways capaciteit? Moet u upgraden naar de volgende hogere SKU?
- Wat zijn de meest in gesprek zijnde hosts, via welke VPN-gateway, over welke poort?
    - Is dit patroon normaal? Selecteer **Alles weergeven** onder **VPN-gateway,** zoals in de volgende afbeelding wordt weergegeven:

        ![Dashboard met de beste actieve VPN-verbindingen](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- In de volgende afbeelding ziet u tijdtrending voor capaciteitsbenutting van een Azure VPN-gateway en de stroomgerelateerde details (zoals toegestane stromen en poorten):

    ![Trend- en stroomdetails van VPN-gateways](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Verkeersverdeling visualiseren op basis van geografie

**Zoeken**

- Verkeersdistributie per datacenter, zoals topbronnen van verkeer naar een datacenter, top malafide netwerken die in gesprek zijn met het datacenter en topprotocollen voor het praten met toepassingen.
  - Als u meer belasting op een datacenter waarneemt, u plannen voor een efficiënte verkeersverdeling.
  - Als malafide netwerken in het datacenter in gesprek zijn, corrigeer dan NSG-regels om ze te blokkeren.

    Selecteer **Kaart weergeven** onder **Uw omgeving,** zoals in de volgende afbeelding wordt weergegeven:

    ![Dashboard met verkeersverdeling](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- De geokaart toont het bovenste lint voor de selectie van parameters zoals datacenters (Geïmplementeerd/Geen implementatie/Actief/Inactief/Traffic Analytics Ingeschakeld/Traffic Analytics Niet ingeschakeld) en landen/regio's die goedaardig/kwaadaardig verkeer bijdragen aan de actieve implementatie:

    ![Geokaartweergave met actieve implementatie](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- De geo-kaart toont de verkeersdistributie naar een datacenter uit landen/regio's en continenten die ermee communiceren in blauwe (Goedaardig verkeer) en rode (kwaadaardig verkeer) gekleurde lijnen:

    ![Geokaartweergave met verkeersverdeling naar landen/regio's en continenten](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Stroomgegevens voor verkeersdistributie in logboekzoekopdrachten](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Verkeersdistributie visualiseren via virtuele netwerken

**Zoeken**

- Verkeersdistributie per virtueel netwerk, topologie, topbronnen van verkeer naar het virtuele netwerk, top malafide netwerken die in gesprek zijn met het virtuele netwerk en top-gesprekstoepassingsprotocollen.
  - Weten welk virtueel netwerk in gesprek is met welk virtueel netwerk. Als het gesprek niet wordt verwacht, kan het worden gecorrigeerd.
  - Als malafide netwerken in gesprek zijn met een virtueel netwerk, u NSG-regels corrigeren om de malafide netwerken te blokkeren.
 
    Selecteer **VNets weergeven** onder **Uw omgeving,** zoals in de volgende afbeelding wordt weergegeven:

    ![Dashboard met virtuele netwerkdistributie](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- De virtuele netwerktopologie toont het bovenste lint voor de selectie van parameters zoals die van een virtueel netwerk (Inter virtual network Connections/Active/Inactive), Externe verbindingen, actieve stromen en kwaadaardige stromen van het virtuele netwerk.
- U de topologie van het virtuele netwerk filteren op basis van abonnementen, werkruimten, resourcegroepen en tijdsinterval. Extra filters die u helpen de stroom te begrijpen zijn: Flow Type (InterVNet, IntraVNET, enzovoort), Flow Direction (Inbound, Outbound), Flow Status (Toegestaan, Geblokkeerd), VNETs (Gericht en Verbonden), Verbindingstype (Peering of Gateway - P2S en S2S) en NSG. Gebruik deze filters om u te concentreren op VNets die u in detail wilt onderzoeken.
- De virtuele netwerktopologie toont de verkeersverdeling naar een virtueel netwerk met betrekking tot stromen (toegestaan/geblokkeerd/Binnenkomend/uitgaand/goedaardig/kwaadaardig), toepassingsprotocol en netwerkbeveiligingsgroepen, bijvoorbeeld:

    ![Virtuele netwerktopologie met verkeersdistributie- en stroomdetails](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Virtuele netwerktopologie met bovenniveau en meer filters](./media/traffic-analytics/virtual-network-filters.png)

    ![Stroomgegevens voor virtuele netwerkverkeerdistributie in logboekzoekopdrachten](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Zoeken**

- Verkeersverdeling per subnet, topologie, topbronnen van verkeer naar het subnet, top malafide netwerken die in gesprek zijn met het subnet en top-gesprekstoepassingsprotocollen.
    - Weten welk subnet in gesprek is met welk subnet. Als u onverwachte gesprekken ziet, u uw configuratie corrigeren.
    - Als malafide netwerken in gesprek zijn met een subnet, bent u in staat om het te corrigeren door het configureren van NSG regels om de malafide netwerken te blokkeren.
- De subnetstopologie toont het bovenste lint voor de selectie van parameters zoals actief/inactief subnet, externe verbindingen, actieve stromen en kwaadaardige stromen van het subnet.
- De subnettopologie toont de verkeersverdeling naar een virtueel netwerk met betrekking tot stromen (Toegestaan/Geblokkeerd/Binnenkomend/Uitgaand/Goedaardig/Kwaadaardig), toepassingsprotocol en NSG's, bijvoorbeeld:

    ![Subnettopologie die verkeersdistributie een virtueel netwerksubnet toont met betrekking tot stromen](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Zoeken**

Verkeersdistributie per Application gateway & Load Balancer, topologie, topbronnen van verkeer, top malafide netwerken die in gesprek zijn met de Application gateway & Load Balancer en top-conversing-toepassingsprotocollen. 
    
 - Weten welk subnet in gesprek is met welke Application gateway of Load Balancer. Als u onverwachte gesprekken observeert, u uw configuratie corrigeren.
 - Als malafide netwerken in gesprek zijn met een Application gateway of Load Balancer, u deze corrigeren door NSG-regels te configureren om de malafide netwerken te blokkeren. 

    ![subnet-topologie-presentatie-verkeer-distributie-naar-een-applicatie-gateway-subnet-met-regards-to-flows](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Bekijk poorten en virtuele machines die verkeer ontvangen van het internet

**Zoeken**

- Welke open poorten zijn in gesprek via het internet?
  - Als er onverwachte poorten worden gevonden die zijn geopend, u uw configuratie corrigeren:

    ![Dashboard met poorten die verkeer ontvangen en verzenden naar het internet](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Details van Azure-doelpoorten en -hosts](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Zoeken**

Heeft u kwaadaardig verkeer in uw omgeving? Waar komt het vandaan? Waar is het bestemd voor?

![Details van kwaadaardig verkeer in logboekzoekopdrachten](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Visualiseer de trends in NSG/NSG regels hits

**Zoeken**

- Welke NSG/NSG regels hebben de meeste hits in vergelijkende grafiek met stromen verdeling?
- Wat zijn de beste bron- en bestemmingsgespreksparen per NSG/NSG-regels?

    ![Dashboard presentatie NSG hits statistieken](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- De volgende foto's tonen tijdtrending voor hits van NSG-regels en bron-bestemmingsstroomdetails voor een netwerkbeveiligingsgroep:

  - Snel detecteren welke NSG- en NSG-regels kwaadaardige stromen doorkruisen en welke de belangrijkste kwaadaardige IP-adressen zijn die toegang hebben tot uw cloudomgeving
  - Bepalen welke NSG/NSG-regels aanzienlijk netwerkverkeer toestaan/blokkeren
  - Selecteer topfilters voor gedetailleerde inspectie van een NSG- of NSG-regels

    ![Presentatie van de trending tijd voor NSG regel hits en top NSG regels](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Top NSG regels statistieken details in log zoeken](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

Zie [Veelgestelde vragen](traffic-analytics-faq.md)over verkeersanalyses voor antwoorden op veelgestelde vragen.

## <a name="next-steps"></a>Volgende stappen

- Zie [NSG-stroomlogboekregistratie inschakelen](network-watcher-nsg-flow-logging-portal.md)voor meer informatie over het inschakelen van stroomlogboeken.
- Zie [Verkeersanalyseschema](traffic-analytics-schema.md)voor het schema en de verwerking van de details van Traffic Analytics.
