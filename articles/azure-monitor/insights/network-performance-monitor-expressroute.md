---
title: Oplossing voor netwerkprestatiemonitor in Azure Log Analytics | Microsoft Documenten
description: Gebruik de ExpressRoute Monitor-mogelijkheid in netwerkprestatiemonitor om de end-to-end-connectiviteit en -prestaties tussen uw filialen en Azure via Azure ExpressRoute te controleren.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 6ac610d7dcf9849b6b439741957684867b9d01aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660747"
---
# <a name="expressroute-monitor"></a>ExpressRoute-monitor

U de Azure ExpressRoute Monitor-mogelijkheid in [netwerkprestatiemonitor](network-performance-monitor.md) gebruiken om end-to-end-connectiviteit en -prestaties tussen uw filialen en Azure via Azure ExpressRoute te controleren. De belangrijkste voordelen zijn: 

- Automatisch detecteren van ExpressRoute-circuits die zijn gekoppeld aan uw abonnement.
- Het bijhouden van bandbreedtegebruik, verlies en latentie op het circuit,peering en Azure Virtual Network-niveau voor ExpressRoute.
- Ontdekking van netwerktopologie van uw ExpressRoute-circuits.

![ExpressRoute-monitor](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Configuratie 
Als u de configuratie voor netwerkprestatiemonitor wilt openen, opent u de [oplossing netwerkprestatiemeter](network-performance-monitor.md) en selecteert **u Configureren**.

### <a name="configure-network-security-group-rules"></a>Groepsregels voor netwerkbeveiliging configureren 
Voor de servers in Azure die worden gebruikt voor bewaking via Network Performance Monitor, configureert u NSG-regels (Network Security Group) om TCP-verkeer toe te staan op de poort die wordt gebruikt door Network Performance Monitor voor synthetische transacties. De standaardpoort is 8084. Met deze configuratie kan de Log Analytics-agent die is geïnstalleerd op Azure VM's communiceren met een on-premises monitoringagent. 

Zie [Netwerkbeveiligingsgroepen](../../virtual-network/manage-network-security-group.md)voor meer informatie over NSG's . 

>[!NOTE]
> Voordat u verdergaat met deze stap, installeert u de on-premises serveragent en de Azure-serveragent en voert u het PowerShell-script enablerules.ps1 uit. 

 
### <a name="discover-expressroute-peering-connections"></a>Ontdek ExpressRoute-peeringverbindingen 
 
1. Selecteer de weergave **ExpressRoute Peerings.**
2. Selecteer **Nu ontdekken** om alle privé-peeringen van ExpressRoute te ontdekken die zijn verbonden met de virtuele netwerken in het Azure-abonnement dat is gekoppeld aan deze Azure Log Analytics-werkruimte.

    >[!NOTE]
    > De oplossing ontdekt momenteel alleen ExpressRoute private peerings. 

    >[!NOTE]
    > Alleen privé-peerings die zijn gekoppeld aan de virtuele netwerken die zijn gekoppeld aan het abonnement dat is gekoppeld aan deze Log Analytics-werkruimte, worden ontdekt. Als ExpressRoute is verbonden met virtuele netwerken buiten het abonnement dat aan deze werkruimte is gekoppeld, maakt u een werkruimte Log Analytics in die abonnementen. Gebruik vervolgens Network Performance Monitor om die peerings te controleren. 

    ![ExpressRoute Monitor-configuratie](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   Nadat de detectie is voltooid, worden de gedetecteerde privépeerverbindingen in een tabel weergegeven. De controle voor deze peerings is in eerste instantie uitgeschakeld. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Controle van de ExpressRoute-peeringverbindingen inschakelen 

1. Selecteer de privé-peeringverbinding die u wilt controleren.
2. Schakel in het deelvenster aan de rechterkant het selectievakje **Monitor dit peering** in. 
3. Als u van plan bent gezondheidsgebeurtenissen voor deze verbinding te maken, selecteert u **Statuscontrole inschakelen voor deze peering**. 
4. Kies controlevoorwaarden. U aangepaste drempelwaarden instellen voor het genereren van statusgebeurtenissen door drempelwaarden in te voeren. Wanneer de waarde van de voorwaarde boven de geselecteerde drempelwaarde voor de peeringverbinding komt, wordt een statusgebeurtenis gegenereerd. 
5. Selecteer **Agents toevoegen** om de bewakingsagents te kiezen die u wilt gebruiken voor het bewaken van deze peeringverbinding. Zorg ervoor dat u agents aan beide uiteinden van de verbinding toevoegt. U hebt ten minste één agent in het virtuele netwerk nodig dat is verbonden met deze peering. U hebt ook ten minste één on-premises agent nodig die is verbonden met deze peering. 
6. Selecteer **Opslaan** om de configuratie op te slaan. 

   ![ExpressRoute-bewakingsconfiguratie](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Nadat u de regels hebt ingeschakeld en waarden en agents hebt geselecteerd, wacht u 30 tot 60 minuten tot de waarden worden ingevuld en de tegels **voor het bewaken van ExpressRoute** worden weergegeven. Wanneer u de bewakingstegels ziet, worden uw ExpressRoute-circuits en verbindingsbronnen nu gecontroleerd door network performance monitor. 

>[!NOTE]
> Deze mogelijkheid werkt betrouwbaar op werkruimten die zijn geüpgraded naar de nieuwe querytaal.

## <a name="walkthrough"></a>Kennismaking 

Het dashboard van de Netwerkprestatiemonitor toont een overzicht van de status van ExpressRoute-circuits en peering-verbindingen. 

![Dashboard netwerkprestatiemonitor](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Lijst met circuits 

Als u een lijst met alle bewaakte ExpressRoute-circuits wilt bekijken, selecteert u de tegel ExpressRoute-circuits. U een circuit selecteren en de status ervan weergeven, trenddiagrammen voor pakketverlies, bandbreedtegebruik en latentie. De grafieken zijn interactief. U een aangepast tijdvenster selecteren voor het plotten van de grafieken. Sleep de muis over een gebied in de grafiek om in te zoomen en zie fijnkorrelige gegevenspunten. 

![ExpressRoute-circuitslijst](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Trends van verlies, latentie en doorvoer 

De bandbreedtegebruiks-, latentie- en verliesgrafieken zijn interactief. U inzoomen op een gedeelte van deze grafieken met muisbesturingselementen. U ook de bandbreedte-, latentie- en verliesgegevens voor andere intervallen bekijken. Selecteer **Datum/tijd**linksboven onder de knop **Acties** . 

![ExpressRoute-latentie](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Peeringslijst 

Als u een lijst met alle verbindingen met virtuele netwerken wilt weergeven via privépeering, selecteert u de tegel **Privé-peerings** op het dashboard. Hier u een virtuele netwerkverbinding selecteren en de status ervan bekijken, trenddiagrammen voor pakketverlies, bandbreedtegebruik en latentie. 

![ExpressRoute-peerings](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Circuittopologie 

Als u de topologie van het circuit wilt weergeven, selecteert u de tegel **Topologie.** Met deze actie bedraagt u de topologieweergave van het geselecteerde circuit of peering. Het topologiediagram biedt de latentie voor elk segment in het netwerk en elke laag 3-hop wordt weergegeven door een knooppunt van het diagram. Het selecteren van een hop onthult meer details over de hop. Als u het zichtbaarheidsniveau wilt verhogen om on-premises hop op te nemen, verplaatst u de schuifbalk onder **FILTERS**. Als u de schuifbalk naar links of rechts verplaatst, neemt het aantal hop in de topologiegrafiek toe of wordt deze afgenomen. De latentie in elk segment is zichtbaar, waardoor segmenten met hoge latentie op uw netwerk sneller kunnen worden geïsoleerd.

![ExpressRoute-topologie](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Gedetailleerde topologieweergave van een circuit 

Deze weergave toont virtuele netwerkverbindingen. 

![Virtuele netwerkverbindingen expressroute](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnostiek 

Met network performance monitor u verschillende problemen met de circuitconnectiviteit diagnosticeren. Enkele van de problemen die u zien zijn hieronder vermeld.

U de meldingscodes bekijken en waarschuwingen op deze codes instellen via **LogAnalytics.** Op de pagina **NPM Diagnostics** ziet u beschrijvingen voor elk diagnostisch bericht dat wordt geactiveerd.

| Meldingscode (logboeken) | Beschrijving |
| --- | --- |
| 5501 | Kan niet doorkruisen via secundaire verbinding van het ExpressRoute-circuit |
| 5502 | Kan niet doorkruisen via de primaire verbinding van het ExpressRoute-circuit |
| 5503 | Er wordt geen circuit gevonden voor een abonnement dat is gekoppeld aan de werkruimte | 
| 5508 | Niet in staat om te bepalen of het verkeer door een circuit(en) voor pad gaat |
| 5510 | Het verkeer rijdt niet door het beoogde circuit | 
| 5511 | Het verkeer gaat niet door het beoogde virtuele netwerk | 

**Het circuit is uitgevallen.** Network Performance Monitor waarschuwt u zodra de verbinding tussen uw on-premises resources en Azure virtuele netwerken verloren gaat. Met deze melding u proactief optreden voordat u gebruikersescalaties ontvangt en downtime vermindert.

![ExpressRoute circuit is uitgevallen](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**Verkeer dat niet door het beoogde circuit stroomt.** Network Performance Monitor waarschuwt u wanneer het verkeer niet door het beoogde ExpressRoute-circuit stroomt. Dit probleem kan optreden als het circuit is uitgeschakeld en het verkeer door de back-uproute stroomt. Het kan ook gebeuren als er een routeringsprobleem is. Met deze informatie u proactief configuratieproblemen in uw routeringsbeleid beheren en ervoor zorgen dat de meest optimale en veilige route wordt gebruikt. 

 

**Verkeer dat niet door het primaire circuit stroomt.** De Network Performance Monitor waarschuwt u wanneer het verkeer via het secundaire ExpressRoute-circuit stroomt. Hoewel u in dit geval geen verbindingsproblemen ondervindt, u zich door het proactief oplossen van de problemen met het primaire circuit beter voorbereiden. 

 
![ExpressRoute verkeersstroom](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Degradatie als gevolg van piekgebruik.** U de trend voor bandbreedtegebruik correleren met de latentietrend om te bepalen of de azure-workloaddegradatie te wijten is aan een piek in bandbreedtegebruik of niet. Dan u dienovereenkomstig actie ondernemen.

![Gebruik van expressroute-bandbreedte](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Volgende stappen
[Zoeklogboeken](../../azure-monitor/log-query/log-query-overview.md) om gedetailleerde netwerkprestatiegegevensrecords weer te geven.
