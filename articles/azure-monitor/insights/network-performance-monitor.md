---
title: Oplossing voor netwerkprestatiemonitor in Azure | Microsoft Documenten
description: Netwerkprestatiemonitor in Azure helpt u de prestaties van uw netwerken in bijna realtime te controleren om knelpunten in netwerkprestaties op te sporen en te lokaliseren.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 02/20/2018
ms.openlocfilehash: 9660e87f3ee4e1c1c6a270f14928fdd111664e66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480875"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Oplossing voor netwerkprestatiemonitor in Azure

![Symbool netwerkprestatiemonitor](./media/network-performance-monitor/npm-symbol.png)


Network Performance Monitor is een cloudgebaseerde hybride netwerkbewakingsoplossing waarmee u de netwerkprestaties tussen verschillende punten in uw netwerkinfrastructuur bewaken. Daarnaast kunt u hiermee de netwerkverbinding met de service en toepassingseindpunten bewaken, evenals de prestaties van Azure ExpressRoute. 

Netwerkprestatiemonitor detecteert netwerkproblemen zoals traffic blackholing, routeringsfouten en problemen die conventionele netwerkbewakingsmethoden niet kunnen detecteren. De oplossing genereert waarschuwingen en waarschuwt u als een drempelwaarde voor een netwerkverbinding wordt overschreden. Bovendien worden problemen met de netwerkprestaties tijdig gedetecteerd en wordt de oorzaak van het probleem op een bepaald netwerksegment of apparaat opgespoord. 

Network Performance Monitor biedt drie brede mogelijkheden: 

* [Prestatiemonitor:](network-performance-monitor-performance-monitor.md)u de netwerkconnectiviteit bewaken voor cloudimplementaties en on-premises locaties, meerdere datacenters en filialen en bedrijfskritische multitier-toepassingen of microservices. Met Performance Monitor u netwerkproblemen detecteren voordat gebruikers klagen.

* [Serviceconnectivity Monitor:](network-performance-monitor-service-connectivity.md)U de connectiviteit van uw gebruikers met de services die u belangrijk vindt controleren, bepalen welke infrastructuur zich in het pad bevindt en bepalen waar netwerkknelpunten optreden. U vóór uw gebruikers op de hoogte zijn van storingen en de exacte locatie van de problemen langs uw netwerkpad bekijken. 

    Met deze mogelijkheid u tests uitvoeren op basis van HTTP, HTTPS, TCP en ICMP om in bijna realtime of historisch gezien de beschikbaarheid en responstijd van uw service te controleren. U ook de bijdrage van het netwerk in pakketverlies en latentie controleren. Met een netwerktopologiekaart u netwerkvertragingen isoleren. U probleemplekken identificeren die zich langs het netwerkpad voordoen, van het knooppunt naar de service, met latentiegegevens op elke hop. Met ingebouwde tests u de netwerkconnectiviteit met Office 365 en Dynamics CRM zonder voorconfiguratie controleren. Met deze mogelijkheid u de netwerkconnectiviteit met elk endpoint met TCP-functionaliteit, zoals websites, SaaS-toepassingen, PaaS-toepassingen en SQL-databases, controleren.

* [ExpressRoute-monitor:](network-performance-monitor-expressroute.md)monitor end-to-end connectiviteit en prestaties tussen uw filialen en Azure, via Azure ExpressRoute.  

Meer informatie over de verschillende mogelijkheden ondersteund door [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) is online beschikbaar.
 
## <a name="supported-regions"></a>Ondersteunde regio's
NPM kan de connectiviteit tussen netwerken en toepassingen in elk deel van de wereld bewaken vanuit een werkruimte die wordt gehost in een van de volgende regio's:
* Europa - noord
* Europa -west
* Frankrijk - centraal
* Canada - midden
* VS - west
* VS - west-centraal
* VS - noord-centraal
* VS - zuid-centraal
* VS - centraal
* VS - oost
* VS - oost 2
* VS - west 2
* Oost-Japan
* Azië - zuidoost
* Zuidoost-Australië
* Australië - centraal
* Australië - oost
* Zuid-VERENIGD Koninkrijk
* Azië - oost
* Korea - centraal
* India - centraal
* Amerikaanse regering Virginia
* China Oost 2


De lijst met ondersteunde regio's voor ExpressRoute Monitor is beschikbaar in de [documentatie.](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117)


## <a name="set-up-and-configure"></a>Instellen en configureren

### <a name="install-and-configure-agents"></a>Agents installeren en configureren 

Gebruik de basisprocessen om agents te installeren bij [Connect Windows-computers met Azure Monitor](../platform/agent-windows.md) en Connect Operations Manager met Azure [Monitor](../platform/om-agents.md).

### <a name="where-to-install-the-agents"></a>Waar de agenten te installeren 

* **Prestatiemonitor:** Installeer Log Analytics-agents op ten minste één knooppunt dat is verbonden met elk subnetwerk van waaruit u de netwerkverbinding met andere subnetwerken wilt controleren.

    Als u een netwerkkoppeling wilt controleren, installeert u agents op beide eindpunten van die koppeling. Als u niet zeker bent over de topologie van uw netwerk, installeert u de agents op servers met kritieke workloads waartussen u de netwerkprestaties wilt controleren. Als u bijvoorbeeld de netwerkverbinding tussen een webserver en een server met SQL wilt controleren, installeert u een agent op beide servers. Agents bewaken netwerkconnectiviteit (koppelingen) tussen hosts, niet de hosts zelf. 

* **Serviceconnectivity Monitor:** Installeer een Log Analytics-agent op elk knooppunt van waaruit u de netwerkverbinding met het serviceeindpunt wilt controleren. Een voorbeeld hiervan is als u de netwerkconnectiviteit met Office 365 wilt controleren vanaf uw kantoorsites met het label O1, O2 en O3. Installeer de agent Log Analytics op ten minste één knooppunt in O1, O2 en O3. 

* **ExpressRoute-monitor:** installeer ten minste één Log Analytics-agent in uw virtuele Azure-netwerk. Installeer ook ten minste één agent in uw on-premises subnetwerk, dat is verbonden via ExpressRoute private peering.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Log Analytics-agents configureren voor bewaking 

Network Performance Monitor maakt gebruik van synthetische transacties om de netwerkprestaties tussen bron- en doelagents te bewaken. U kiezen tussen TCP en ICMP als het protocol voor monitoring in de mogelijkheden voor prestatiemeter en serviceconnectiviteitsmonitor. Alleen TCP is beschikbaar als monitoringprotocol voor ExpressRoute Monitor. Zorg ervoor dat de firewall communicatie mogelijk maakt tussen de Log Analytics-agents die worden gebruikt voor het controleren van het protocol dat u kiest. 

* **TCP-protocol**: Als u TCP kiest als protocol voor controle, opent u de firewallpoort op de agents die worden gebruikt voor Network Performance Monitor en ExpressRoute Monitor om ervoor te zorgen dat de agents verbinding met elkaar kunnen maken. Als u de poort wilt openen, voert u het [PowerShell-script enablerules.ps1](https://aka.ms/npmpowershellscript) uit zonder parameters in een PowerShell-venster met beheerdersbevoegdheden.

    Het script maakt registersleutels die door de oplossing zijn vereist. Het maakt ook Windows Firewall-regels waarmee agents TCP-verbindingen met elkaar kunnen maken. De registersleutels die door het script zijn gemaakt, geven aan of de foutopsporingslogboeken en het pad voor het logboekbestand moeten worden geregistreerd. Het script definieert ook de agent TCP-poort die wordt gebruikt voor communicatie. De waarden voor deze toetsen worden automatisch ingesteld door het script. Verander deze toetsen niet handmatig. De standaard geopende poort is 8084. U een aangepaste poort gebruiken door de parameterpoortNummer aan het script op te geven. Gebruik dezelfde poort op alle computers waar het script wordt uitgevoerd. 

    >[!NOTE]
    > Het script configureert alleen Windows Firewall lokaal. Als u een netwerkfirewall hebt, moet u ervoor zorgen dat verkeer dat is bestemd voor de TCP-poort die wordt gebruikt door Network Performance Monitor, wordt gebruikt.

    >[!NOTE]
    > U hoeft het [EnableRules.ps1 PowerShell-script](https://aka.ms/npmpowershellscript ) voor Service Connectivity Monitor niet uit te voeren.

    

* **ICMP-protocol:** Als u ICMP kiest als protocol voor bewaking, schakelt u de volgende firewallregels in om ICMP betrouwbaar te gebruiken:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>De oplossing configureren 

1. Voeg de oplossing netwerkprestatiemeter toe aan uw werkruimte vanuit de [Azure-marktplaats.](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) U ook het proces gebruiken dat is beschreven in [Azure-monitoroplossingen toevoegen vanuit de galerie Oplossingen.](../../azure-monitor/insights/solutions.md) 
2. Open de werkruimte Log Analytics en selecteer de tegel **Overzicht.** 
3. Selecteer de tegel **Netwerkprestatiemonitor** met het bericht *Oplossing vereist extra configuratie*.

   ![Tegel netwerkprestatiemonitor](media/network-performance-monitor/npm-config.png)

4. Op de pagina **Setup** ziet u de optie om Log Analytics-agents te installeren en de agents te configureren voor controle in de weergave **Algemene instellingen.** Als u loganalytics-agents hebt geïnstalleerd en geconfigureerd, selecteert **u,** zoals eerder is uitgelegd, de installatieweergave om de gewenste capaciteit te configureren. 

   **Prestatiemonitor:** kies het protocol dat u wilt gebruiken voor synthetische transacties in de regel **Standaardprestatiemonitor** en selecteer **Opslaan & Doorgaan**. Deze protocolselectie geldt alleen voor de standaardregel die door het systeem wordt gegenereerd. U moet het protocol kiezen telkens wanneer u een prestatiemonitorregel expliciet maakt. U altijd naar de **standaardregelinstellingen** gaan op het tabblad **Prestatiemonitor** (deze wordt weergegeven nadat u uw dag-0-configuratie hebt voltooid) en het protocol later wijzigen. Als u de mogelijkheid prestatiemeter niet wilt, u de standaardregel uitschakelen via de **standaardregelinstellingen** op het tabblad **Prestatiemonitor.**

   ![Prestatiemonitorweergave](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Serviceconnectivity Monitor**: De mogelijkheid biedt ingebouwde vooraf geconfigureerde tests om de netwerkconnectiviteit met Office 365 en Dynamics 365 van uw agents te controleren. Kies de Office 365- en Dynamics 365-services die u wilt controleren door de selectievakjes ernaast in te schakelen. Als u de agents wilt kiezen waaruit u wilt controleren, selecteert u **Agents toevoegen**. Als u deze mogelijkheid niet wilt gebruiken of deze later wilt instellen, kiest u niets en selecteert **u Opslaan & Doorgaan**.

   ![Weergave Serviceconnectivity Monitor](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **ExpressRoute-monitor**: selecteer **Nu ontdekken** om alle privé-peeringen van ExpressRoute te ontdekken die zijn verbonden met de virtuele netwerken in het Azure-abonnement dat is gekoppeld aan deze Log Analytics-werkruimte. 

   ![ExpressRoute Monitor weergave](media/network-performance-monitor/npm-express-route.png)

   Nadat de ontdekking is voltooid, worden de gedetecteerde circuits en peerings weergegeven in een tabel. 

   ![Pagina Configuratie van netwerkprestatiemonitor](media/network-performance-monitor/npm-private-peerings.png)
    
De bewaking voor deze circuits en peerings is in eerste instantie uitgeschakeld. Selecteer elke resource die u wilt controleren en configureer de controle voor deze resources vanuit de detailsweergave aan de rechterkant. Selecteer **Opslaan** om de configuratie op te slaan. Zie het artikel 'ExpressRoute-controle configureren' voor meer informatie. 

Nadat de installatie is voltooid, duurt het 30 minuten tot een uur voordat de gegevens zijn gevuld. Terwijl de oplossing gegevens uit uw netwerk verzamelt, ziet u de *berichtoplossing die extra configuratie vereist* op de tegel **Overzicht** van de netwerkprestatiemonitor. Nadat de gegevens zijn verzameld en geïndexeerd, wordt de tegel **Overzicht** gewijzigd en wordt u in een samenvatting geïnformeerd over de status van uw netwerk. Vervolgens u de bewaking bewerken van de knooppunten waarop Log Analytics-agents zijn geïnstalleerd, evenals de subnetten die vanuit uw omgeving worden ontdekt.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Controle-instellingen voor subnetten en knooppunten bewerken 

Alle subnetten met ten minste één agent geïnstalleerd, worden vermeld op het tabblad **Subnetwerken** op de configuratiepagina. 


Ga als volgt te werk om de bewaking van bepaalde subnetwerken in of uit te schakelen:

1. Schakel het selectievakje naast de **subnetwerk-id**in of uit . Zorg er dan voor dat **Gebruik voor monitoring** is geselecteerd of gewist, indien van toepassing. U meerdere subnetten selecteren of wissen. Wanneer uitgeschakeld, subnetwerken worden niet gecontroleerd en de agents worden bijgewerkt om te stoppen pingen andere agenten. 
2. Kies de knooppunten die u in een bepaald subnetwerk wilt controleren. Selecteer het subnetwerk in de lijst en verplaats de vereiste knooppunten tussen de lijsten die niet-bewaakte en bewaakte knooppunten bevatten. U een aangepaste beschrijving toevoegen aan het subnetwerk.
3. Selecteer **Opslaan** om de configuratie op te slaan. 

#### <a name="choose-nodes-to-monitor"></a>Knooppunten kiezen om te controleren

Alle knooppunten waarop een agent is geïnstalleerd, worden weergegeven op het tabblad **Knooppunten.** 

1. Selecteer of schakel de knooppunten uit die u wilt controleren of stop met de bewaking. 
2. Selecteer **Gebruiken voor controle**of indien van toepassing wissen. 
3. Selecteer **Opslaan**. 


Configureer de gewenste mogelijkheden:

- [Prestatiemeter](network-performance-monitor-performance-monitor.md#configuration)
- [Bewaking van serviceverbinding](network-performance-monitor-performance-monitor.md#configuration)
- [ExpressRoute-monitor](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Gegevensverzamelingsgegevens
Om gegevens over verlies en latentie te verzamelen, gebruikt Network Performance Monitor TCP SYN-SYNACK-ACK handshake-pakketten wanneer u TCP als protocol kiest. Network Performance Monitor maakt gebruik van ICMP ECHO ICMP ECHO REPLY wanneer u ICMP als protocol kiest. Trace route wordt ook gebruikt om topologie informatie te krijgen.

In de volgende tabel worden methoden voor het verzamelen van gegevens en andere details weergegeven over de manier waarop gegevens worden verzameld voor netwerkprestatiemeter.

| Platform | Direct agent | Agent Operations Manager van Systeemcentrum | Azure Storage | Operations Manager vereist? | Operations Manager agent gegevens verzonden via management groep | Verzamelingsfrequentie |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP handshakes/ICMP ECHO berichten om de 5 seconden, gegevens verzonden om de 3 minuten |
 

 
De oplossing maakt gebruik van synthetische transacties om de gezondheid van het netwerk te beoordelen. Log Analytics-agents die op verschillende punten in de TCP-pakketten van de netwerkuitwisseling of ICMP Echo met elkaar zijn geïnstalleerd. Of de agents TCP-pakketten of ICMP Echo gebruiken, hangt af van het protocol dat u hebt geselecteerd voor controle. In het proces, agenten leren de heen-en-terug tijd en pakket verlies, indien van toepassing. Periodiek voert elke agent ook een traceroute uit naar andere agenten om alle verschillende routes in het netwerk te vinden die moeten worden getest. Met behulp van deze gegevens kunnen de agents de netwerklatentie en pakketverliescijfers afleiden. De tests worden elke vijf seconden herhaald. Gegevens worden ongeveer drie minuten samengevoegd door de agents voordat ze worden geüpload naar de werkruimte Log Analytics in Azure Monitor.



>[!NOTE]
> Hoewel agenten vaak met elkaar communiceren, genereren ze geen significant netwerkverkeer tijdens het uitvoeren van de tests. Agents vertrouwen alleen op TCP SYN-SYNACK-ACK handshake pakketten om het verlies en de latentie te bepalen. Er worden geen gegevenspakketten uitgewisseld. Tijdens dit proces communiceren agenten alleen met elkaar wanneer dat nodig is. De communicatietopologie van de agent is geoptimaliseerd om het netwerkverkeer te verminderen.

## <a name="use-the-solution"></a>Gebruik de oplossing 

### <a name="network-performance-monitor-overview-tile"></a>Overzichtsoverzicht van netwerkprestatiemonitor 

Nadat u de oplossing netwerkprestatiemeter hebt ingeschakeld, biedt de oplossingstegel op de pagina **Overzicht** een snel overzicht van de netwerkstatus. 

 ![Overzichtsoverzicht van netwerkprestatiemonitor](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Dashboard netwerkprestatiemonitor 

* **Top netwerkstatusgebeurtenissen:** deze pagina bevat een lijst met de meest recente statusgebeurtenissen en waarschuwingen in het systeem en de tijd sinds de gebeurtenissen actief zijn. Een statusgebeurtenis of waarschuwing wordt gegenereerd wanneer de waarde van de gekozen statistiek (verlies, latentie, reactietijd of bandbreedtegebruik) voor de bewakingsregel de drempelwaarde overschrijdt. 

* **ExpressRoute Monitor**: Deze pagina bevat gezondheidssamenvattingen voor de verschillende ExpressRoute peering verbindingen de oplossing monitoren. De tegel **Topologie** toont het aantal netwerkpaden via de ExpressRoute-circuits die in uw netwerk worden bewaakt. Selecteer deze tegel om naar de **topologieweergave** te gaan.

* **Service connectivity Monitor:** Deze pagina bevat gezondheidssamenvattingen voor de verschillende tests die u hebt gemaakt. De tegel **Topologie** geeft het aantal eindpunten weer dat wordt gecontroleerd. Selecteer deze tegel om naar de **topologieweergave** te gaan.

* **Prestatiemonitor:** deze pagina bevat statussamenvattingen voor de **netwerkkoppelingen** en **subnetwerkkoppelingen** die de oplossing bewaakt. De tegel **Topologie** toont het aantal netwerkpaden dat in uw netwerk wordt gecontroleerd. Selecteer deze tegel om naar de **topologieweergave** te gaan. 

* **Algemene query's:** Deze pagina bevat een set zoekopdrachten waarmee gegevens voor onbewerkte netwerkbewaking rechtstreeks worden opgehaald. U deze query's gebruiken als uitgangspunt om uw eigen query's te maken voor aangepaste rapportage. 

   ![Dashboard netwerkprestatiemonitor](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Inzoomen op diepte 

U verschillende koppelingen op het oplossingsdashboard selecteren om dieper in te zoomen op elk interessegebied. Wanneer u bijvoorbeeld een waarschuwing of een ongezonde netwerkkoppeling op het dashboard ziet verschijnen, selecteert u deze om deze verder te onderzoeken. Een pagina bevat alle subnetwerkkoppelingen voor de specifieke netwerkkoppeling. U de verlies-, latentie- en statusstatus van elke subnetwerkkoppeling zien. U snel zien welke subnetwerkkoppeling problemen veroorzaakt. Selecteer **Knooppuntkoppelingen weergeven** om alle knooppuntkoppelingen voor de koppeling met een ongezond subnet weer te geven. Vervolgens u afzonderlijke knooppuntkoppelingen bekijken en de ongezonde knooppuntkoppelingen zoeken. 

Selecteer **Topologie weergeven** om de hop-by-hop-topologie van de routes tussen de bron- en doelknooppunten weer te geven. De ongezonde routes worden rood weergegeven. U de latentie bekijken die door elke hop wordt bijgedragen, zodat u het probleem snel identificeren in een bepaald deel van het netwerk.

 

### <a name="network-state-recorder-control"></a>Network State Recorder-besturingselement

Elke weergave geeft een momentopname weer van uw netwerkstatus op een bepaald tijdstip. Standaard wordt de meest recente status weergegeven. De balk boven aan de pagina toont het tijdstip waarvoor de status wordt weergegeven. Als u een momentopname van uw netwerkstatus op een vorige tijd wilt weergeven, selecteert u **Acties**. U ook automatisch vernieuwen voor elke pagina in- of uitschakelen terwijl u de laatste status bekijkt. 

 ![Netwerkstaterecorder](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Trendgrafieken 

Op elk niveau dat u inzoomt, ziet u de trend van de toepasselijke statistiek. Het kan verlies, latentie, reactietijd of bandbreedtegebruik zijn. Als u het tijdsinterval voor de trend wilt wijzigen, gebruikt u het tijdsbesturingselement boven aan de grafiek. 

Trenddiagrammen tonen u een historisch perspectief op de prestaties van een prestatiestatistiek. Sommige netwerkproblemen zijn van voorbijgaande aard en zijn moeilijk te vangen door alleen te kijken naar de huidige status van het netwerk. Problemen kunnen snel opduiken en verdwijnen voordat iemand het merkt, alleen om op een later tijdstip weer te verschijnen. Dergelijke tijdelijke problemen kunnen ook moeilijk zijn voor toepassingsbeheerders. De problemen worden vaak weergegeven als onverklaarbare verhogingen van de responstijd van toepassingen, zelfs wanneer alle toepassingscomponenten probleemloos lijken te werken. 

U dit soort problemen eenvoudig detecteren door naar een trenddiagram te kijken. Het probleem wordt weergegeven als een plotselinge piek in netwerklatentie of pakketverlies. Als u het probleem wilt onderzoeken, gebruikt u het besturingselement Network State Recorder om de netwerkmomentopname en topologie weer te geven voor dat moment waarop het probleem zich heeft voorgedaan.

 
![Trendgrafieken](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Topologiekaart 

Network Performance Monitor toont u de hop-by-hop topologie van routes tussen de bron en het eindpunt van de bestemming op een interactieve topologiekaart. Als u de topologiekaart wilt weergeven, selecteert u de tegel **Topologie** op het oplossingsdashboard. U ook de koppeling **Topologie weergeven** selecteren op de inzoompagina's. 

De topologiekaart geeft aan hoeveel routes er zijn tussen de bron en bestemming en welke paden de gegevenspakketten volgen. De latentie die door elke netwerkhop wordt bijgedragen, is ook zichtbaar. Alle paden waarvoor de totale padlatentie boven de drempelwaarde ligt (ingesteld in de bijbehorende bewakingsregel) worden rood weergegeven. 

Wanneer u een knooppunt selecteert of er boven op de topologiekaart zweeft, ziet u de knooppunteigenschappen, zoals FQDN en IP-adres. Selecteer een hop om het IP-adres weer te geven. U de lastige netwerkhop identificeren door de latentie te merken die het bijdraagt. Als u bepaalde routes wilt filteren, gebruikt u de filters in het inklapbare actievenster. Als u de netwerktopologieën wilt vereenvoudigen, verbergt u de tussenliggende hop met behulp van de schuifregelaar in het actiedeelvenster. U in- of uitzoomen op de topologiekaart met behulp van uw muiswiel. 

De topologie in de kaart is laag 3-topologie en bevat geen laag 2-apparaten en verbindingen. 

 
![Topologiekaart](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Logboekquery's in Azure Monitor

Alle gegevens die grafisch worden weergegeven via het dashboard en de inzoompagina's van de netwerkprestatiemeter, zijn ook native beschikbaar in [logboekquery's.](../log-query/log-query-overview.md) U interactieve gegevensanalyse uitvoeren in de repository en gegevens uit verschillende bronnen correleren. U ook aangepaste waarschuwingen en weergaven maken en de gegevens exporteren naar Excel, Power BI of een deelbare koppeling. Het gebied **Algemene query's** in het dashboard heeft een aantal nuttige query's die u als uitgangspunt gebruiken om uw eigen query's en rapporten te maken. 

## <a name="alerts"></a>Waarschuwingen

Network Performance Monitor maakt gebruik van de waarschuwingsmogelijkheden van [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts).

Dit betekent dat alle meldingen worden beheerd met behulp van [actiegroepen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).  

Als u een NPM-gebruiker bent die een waarschuwing maakt via Log Analytics: 
1. U ziet een koppeling die u doorverwijst naar azure-portal. Klik erop om toegang te krijgen tot de portal.
2. Klik op de tegel netwerkprestatiemeteroplossing. 
3. Navigeer naar Configureren.  
4. Selecteer de test waarop u een waarschuwing wilt maken en volg de onderstaande stappen.

Als u een NPM-gebruiker bent die een waarschuwing maakt via azure-portal:  
1. U ervoor kiezen om uw e-mail rechtstreeks in te voeren of u ervoor kiezen om waarschuwingen te maken via actiegroepen.
2. Als u ervoor kiest uw e-mail rechtstreeks in te voeren, wordt een actiegroep met de naam **NPM Email ActionGroup** gemaakt en wordt de e-id aan die actiegroep toegevoegd.
3. Als u ervoor kiest om actiegroepen te gebruiken, moet u een eerder gemaakte actiegroep selecteren. Hier u leren hoe u een actiegroep [maakt.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#create-an-action-group-by-using-the-azure-portal) 
4. Zodra de waarschuwing is gemaakt, u de koppeling Waarschuwingen beheren gebruiken om uw waarschuwingen te beheren. 

Elke keer dat u een waarschuwing maakt, maakt NPM een op query gebaseerde logboekwaarschuwingsregel in Azure Monitor. Deze query wordt standaard elke 5 minuten geactiveerd. Azure-monitor brengt geen kosten in rekening voor de eerste 250 logboekwaarschuwingsregels die zijn gemaakt en alle waarschuwingsregels boven de limiet voor waarschuwingen voor 250 logboeken worden gefactureerd volgens [de prijzen waarschuwingen op de prijspagina Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
Meldingen worden afzonderlijk in rekening gebracht volgens [de prijzen van meldingen op de prijspagina azure monitor](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="pricing"></a>Prijzen

Informatie over prijzen is [online](network-performance-monitor-pricing-faq.md)beschikbaar.

## <a name="provide-feedback"></a>Feedback geven 

* **UserVoice:** U uw ideeën plaatsen voor functies van network performance monitor waaraan u wilt dat we werken. Ga naar de [UserVoice-pagina](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Word lid van ons cohort:** We zijn altijd geïnteresseerd in het hebben van nieuwe klanten toetreden tot onze cohort. Als onderdeel hiervan krijgt u vroegtijdig toegang tot nieuwe functies en een kans om ons te helpen de netwerkprestatiemonitor te verbeteren. Als u geïnteresseerd bent om deel te nemen, vul dan deze [snelle enquête](https://aka.ms/npmcohort)in. 

## <a name="next-steps"></a>Volgende stappen 
Meer informatie over [Prestatiemonitor,](network-performance-monitor-performance-monitor.md) [Service Connectivity Monitor](network-performance-monitor-performance-monitor.md)en [ExpressRoute Monitor](network-performance-monitor-expressroute.md). 
