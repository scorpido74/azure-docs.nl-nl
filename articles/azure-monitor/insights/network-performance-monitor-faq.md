---
title: Veelgestelde vragen - Oplossing voor netwerkprestatiemonitor in Azure | Microsoft Documenten
description: In dit artikel worden de veelgestelde vragen over netwerkprestatiemeter in Azure vastgelegd. Network Performance Monitor (NPM) helpt u de prestaties van uw netwerken in bijna realtime te monitoren en knelpunten in netwerkprestaties op te sporen en te lokaliseren.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 10/12/2018
ms.openlocfilehash: 443e4b44633e949dd9bd55df1ec7d18ca93d6e04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096222"
---
# <a name="network-performance-monitor-solution-faq"></a>Veelgestelde vragen over netwerkprestatiemonitoroplossing

![Symbool netwerkprestatiemonitor](media/network-performance-monitor-faq/npm-symbol.png)

In dit artikel worden de veelgestelde vragen (veelgestelde vragen) over Network Performance Monitor (NPM) in Azure

[Network Performance Monitor](/azure/networking/network-monitoring-overview) is een cloudgebaseerde [hybride netwerkbewakingsoplossing](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md) waarmee u de netwerkprestaties tussen verschillende punten in uw netwerkinfrastructuur bewaken. Het helpt u ook de netwerkconnectiviteit met [service- en toepassingseindpunten](../../azure-monitor/insights/network-performance-monitor-service-connectivity.md) te controleren en [de prestaties van Azure ExpressRoute te controleren.](../../azure-monitor/insights/network-performance-monitor-expressroute.md) 

Netwerkprestatiemonitor detecteert netwerkproblemen zoals traffic blackholing, routeringsfouten en problemen die conventionele netwerkbewakingsmethoden niet kunnen detecteren. De oplossing genereert waarschuwingen en waarschuwt u als een drempelwaarde voor een netwerkverbinding wordt overschreden. Bovendien worden problemen met de netwerkprestaties tijdig gedetecteerd en wordt de oorzaak van het probleem op een bepaald netwerksegment of apparaat opgespoord. 

Meer informatie over de verschillende mogelijkheden ondersteund door [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) is online beschikbaar.

## <a name="set-up-and-configure-agents"></a>Agents instellen en configureren

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Wat zijn de platformvereisten voor de knooppunten die moeten worden gebruikt voor monitoring door NPM?
Hieronder staan de platformvereisten voor de verschillende mogelijkheden van NPM:

- De mogelijkheden voor prestatiemonitor en serviceconnectiviteitsmonitor van NPM ondersteunen zowel Windows-server als Windows-desktops/client-besturingssystemen. Ondersteunde Windows server OS-versies zijn 2008 SP1 of hoger. Ondersteunde Windows-desktops/clientversies windows 10, Windows 8.1, Windows 8 en Windows 7. 
- De ExpressRoute Monitor-mogelijkheid van NPM ondersteunt alleen het Windows-serverbesturingssysteem (2008 SP1 of hoger).

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Kan ik Linux-machines gebruiken als monitoringnodes in NPM?
De mogelijkheid om netwerken te monitoren met behulp van Linux-gebaseerde knooppunten is momenteel in preview. Neem contact op met je Accountmanager om meer te weten te komen. Linux-agents bieden alleen bewakingsmogelijkheden voor de prestatiesmonitorvan NPM en zijn niet beschikbaar voor de mogelijkheden serviceconnectiviteitsmonitor en ExpressRoute-monitor

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Wat zijn de groottevereisten van de knooppunten die moeten worden gebruikt voor monitoring door NPM?
Voor het uitvoeren van de NPM-oplossing op knooppuntVM's om netwerken te controleren, moeten de knooppunten ten minste 500 MB geheugen en één kern hebben. U hoeft geen afzonderlijke knooppunten te gebruiken voor het uitvoeren van NPM. De oplossing kan worden uitgevoerd op knooppunten waarop andere workloads worden uitgevoerd. De oplossing heeft de mogelijkheid om het bewakingsproces te stoppen als het meer dan 5% CPU gebruikt.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Moet ik mijn knooppunten verbinden als Direct-agent of via System Center Operations Manager om NPM te gebruiken?
Zowel de prestatiemonitor als de serviceconnectiviteitsmonitor ondersteunen knooppunten [die zijn verbonden als Direct Agents](../../azure-monitor/platform/agent-windows.md) en zijn verbonden via Operations [Manager.](../../azure-monitor/platform/om-agents.md)

Voor de mogelijkheid van ExpressRoute Monitor moeten de Azure-knooppunten alleen als direct agents worden verbonden. Azure-knooppunten die zijn verbonden via Operations Manager worden niet ondersteund. Voor on-premises knooppunten worden de knooppunten die zijn verbonden als Direct Agents en via Operations Manager ondersteund voor het bewaken van een ExpressRoute-circuit.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Welk protocol tussen TCP en ICMP moet worden gekozen voor monitoring?
Als u uw netwerk controleert met windows-serverknooppunten, raden we u aan TCP als bewakingsprotocol te gebruiken, omdat dit een betere nauwkeurigheid biedt. 

ICMP wordt aanbevolen voor windows-desktops/client-gebaseerde knooppunten voor besturingssystemen. Dit platform staat niet toe dat TCP-gegevens worden verzonden via raw-sockets, die NPM gebruikt om netwerktopologie te ontdekken.

U meer details over de relatieve voordelen van elk protocol [hier](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md#choose-the-protocol).

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Hoe kan ik een knooppunt configureren om monitoring te ondersteunen met behulp van tcp-protocol?
Ga als volgt te werk om de bewaking te ondersteunen met behulp van het TCP-protocol: 
* Controleer of het knooppuntplatform Windows Server is (2008 SP1 of hoger).
* Voer [EnableRules.ps1](https://aka.ms/npmpowershellscript) Powershell-script uit op het knooppunt. Zie [instructies](../../azure-monitor/insights/network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) voor meer informatie.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Hoe kan ik de TCP-poort wijzigen die door NPM wordt gebruikt voor monitoring?
U de TCP-poort die door NPM wordt gebruikt voor controle wijzigen door het [script EnableRules.ps1](https://aka.ms/npmpowershellscript) uit te voeren. U moet het poortnummer invoeren dat u als parameter wilt gebruiken. Als u bijvoorbeeld TCP op poort 8060 wilt inschakelen, voert u . `EnableRules.ps1 8060` Zorg ervoor dat u dezelfde TCP-poort gebruikt op alle knooppunten die worden gebruikt voor bewaking.

Het script configureert alleen Windows Firewall lokaal. Als u nsg-regels (Network Security Group) hebt, moet u ervoor zorgen dat het verkeer dat is bestemd voor de TCP-poort die door NPM wordt gebruikt, wordt toegestaan.

### <a name="how-many-agents-should-i-use"></a>Hoeveel agenten moet ik gebruiken?
U moet ten minste één agent gebruiken voor elk subnet dat u wilt controleren.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Wat is het maximum aantal agenten die ik kan gebruiken of ik zie fout ".... u uw configuratielimiet hebt bereikt"?
NPM beperkt het aantal IP's tot 5000 IP's per werkruimte. Als een knooppunt zowel IPv4- als IPv6-adressen heeft, telt dit als 2 IP's voor dat knooppunt. Vandaar dat deze limiet van 5000 IP's de bovengrens voor het aantal agenten zou bepalen. U de inactieve agents verwijderen uit het tabblad Knooppunten in NPM >> Configureren. NPM onderhoudt ook de geschiedenis van alle IP's die ooit zijn toegewezen aan de VM hosting van de agent en elk wordt geteld als aparte IP bij te dragen aan die bovengrens van 5000 IP's. Als u IP's voor uw werkruimte wilt vrijmaken, u de pagina Knooppunten gebruiken om de IP's te verwijderen die niet in gebruik zijn.

## <a name="monitoring"></a>Bewaking

### <a name="how-are-loss-and-latency-calculated"></a>Hoe worden verlies en latentie berekend
Bronagents verzenden tcp syn-aanvragen (als TCP wordt gekozen als het protocol voor monitoring) of ICMP ECHO-aanvragen (als ICMP wordt gekozen als het protocol voor monitoring) op regelmatige tijdstippen naar het ip-adres van de bestemming om ervoor te zorgen dat alle paden tussen het IP-adres van de bronbestemming combinatie worden gedekt. Het percentage ontvangen pakketten en de retourtijd van pakketten wordt gemeten om het verlies en de latentie van elk pad te berekenen. Deze gegevens worden samengevoegd over het polling-interval en over alle paden om de geaggregeerde waarden van verlies en latentie voor de IP-combinatie voor het specifieke polling-interval te krijgen.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Met welke frequentie stuurt de bronagent pakketten naar de bestemming voor controle?
Voor de mogelijkheden Prestatiesmonitor en ExpressRoute Monitor verzendt de bron pakketten om de 5 seconden en registreert de netwerkmetingen. Deze gegevens worden samengevoegd over een polling-interval van 3 minuten om de gemiddelde en piekwaarden van verlies en latentie te berekenen. Voor de mogelijkheid van serviceconnectiviteitsmonitor wordt de frequentie van het verzenden van de pakketten voor netwerkmeting bepaald door de frequentie die door de gebruiker is ingevoerd voor de specifieke test tijdens het configureren van de test.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Hoeveel pakketten worden verzonden voor controle?
Het aantal pakketten dat door de bronagent naar bestemming in een polling wordt verzonden, is adaptief en wordt bepaald door ons eigen algoritme, dat voor verschillende netwerktopologieën anders kan zijn. Meer het aantal netwerkpaden tussen de IP-combinatie bronbestemming, meer is het aantal pakketten dat wordt verzonden. Het systeem zorgt ervoor dat alle paden tussen de IP-combinatie bron-bestemming worden bestreken.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Hoe ontdekt NPM netwerktopologie tussen bron en bestemming?
NPM maakt gebruik van een eigen algoritme op basis van Traceroute om alle paden en hop tussen bron en bestemming te ontdekken.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>Biedt NPM route- en schakelniveaugegevens 
Hoewel NPM alle mogelijke routes tussen de bronagent en de bestemming kan detecteren, biedt het geen inzicht in welke route is genomen door de pakketten die door uw specifieke workloads zijn verzonden. De oplossing kan u helpen bij het identificeren van de paden en onderliggende netwerkhops, die meer latentie toevoegen dan u had verwacht.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Waarom zijn sommige paden ongezond?
Er kunnen verschillende netwerkpaden bestaan tussen de bron- en doel-IP's en elk pad kan een andere waarde van verlies en latentie hebben. NPM markeert deze paden als ongezond (aangeduid met rode kleur) waarvoor de waarden van verlies en/of latentie groter zijn dan de respectievelijke drempelwaarde die in de bewakingsconfiguratie is ingesteld.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>Wat betekent een hop in rode kleur in de netwerktopologiekaart?
Als een hop rood is, betekent dit dat deze deel uitmaakt van ten minste één ongezond pad. NPM markeert alleen de paden als ongezond, het scheidt de status van elk pad niet. Om de lastige hop te identificeren, u de hop-by-hop latency bekijken en degenen die meer toevoegen dan verwachte latentie scheiden.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Hoe werkt foutlokalisatie in Performance Monitor?
NPM gebruikt een probabilistisch mechanisme om foutwaarschijnlijkheden toe te wijzen aan elk netwerkpad, netwerksegment en de samenstellende netwerkhop op basis van het aantal ongezonde paden waar ze deel van uitmaken. Naarmate de netwerksegmenten en hops deel gaan uitmaken van meer ongezonde paden, neemt de foutwaarschijnlijkheid die ermee gepaard gaat toe. Dit algoritme werkt het beste wanneer u veel knooppunten met NPM-agent met elkaar hebt verbonden, omdat dit de gegevenspunten voor het berekenen van de foutwaarschijnlijkheid verhoogt.

### <a name="how-can-i-create-alerts-in-npm"></a>Hoe kan ik waarschuwingen maken in NPM?
Raadpleeg [de sectie waarschuwingen in de documentatie](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor#alerts) voor stapsgewijze instructies.

### <a name="what-are-the-default-log-analytics-queries-for-alerts"></a>Wat zijn de standaard query's van Log Analytics voor waarschuwingen
Query prestatiemeter

    NetworkMonitoring 
     | where (SubType == "SubNetwork" or SubType == "NetworkPath") 
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and RuleName == "<<your rule name>>"
    
Query voor serviceconnectiviteitsmonitor

    NetworkMonitoring                 
     | where (SubType == "EndpointHealth" or SubType == "EndpointPath")
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or ServiceResponseHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and TestName == "<<your test name>>"
    
ExpressRoute monitorquery's: vraag naar circuits

    NetworkMonitoring
    | where (SubType == "ERCircuitTotalUtilization") and (UtilizationHealthState == "Unhealthy") and CircuitResourceId == "<<your circuit resource ID>>"

Persoonlijke peering

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ExpressRoutePath")   
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == "<<your circuit name>>" and VirtualNetwork == "<<vnet name>>"

Microsoft-peering

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == ""<<your circuit name>>" and PeeringType == "MicrosoftPeering"

Veelvoorkomende query   

    NetworkMonitoring
    | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") 

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>Kan NPM routers en servers als afzonderlijke apparaten controleren?
NPM identificeert alleen de IP- en hostnaam van onderliggende netwerkhops (switches, routers, servers, enz.) tussen de bron- en doel-IP's. Het identificeert ook de latentie tussen deze geïdentificeerde hop. Het controleert deze onderliggende hop niet individueel.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>Kan NPM worden gebruikt om de netwerkconnectiviteit tussen Azure en AWS te monitoren?
Ja. Raadpleeg het artikel [Azure, AWS en on-premises netwerken met NPM](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/) voor meer informatie.

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>Is de ExpressRoute bandbreedte gebruik inkomende of uitgaande?
Bandbreedtegebruik is het totaal van inkomende en uitgaande bandbreedte. Het wordt uitgedrukt in Bits/sec.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Kunnen we inkomende en uitgaande bandbreedte informatie krijgen voor de ExpressRoute?
Inkomende en uitgaande waarden voor zowel primaire als secundaire bandbreedte kunnen worden vastgelegd.

Gebruik de onderstaande query in Log Search voor informatie over ms-peeringniveau

    NetworkMonitoring 
     | where SubType == "ERMSPeeringUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
    
Gebruik de onderstaande query in Log Search voor informatie over privé-peeringniveau

    NetworkMonitoring 
     | where SubType == "ERVNetConnectionUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
  
Gebruik de onderstaande query voor informatie op circuitniveau in Log Search

    NetworkMonitoring 
        | where SubType == "ERCircuitTotalUtilization"
        | project CircuitName, PrimaryBytesInPerSecond, PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Welke regio's worden ondersteund voor de Prestatiemonitor van NPM?
NPM kan de connectiviteit tussen netwerken in elk deel van de wereld monitoren vanuit een werkruimte die wordt gehost in een van de [ondersteunde regio's](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Welke regio's worden ondersteund voor de Service Connectivity Monitor van NPM?
NPM kan de connectiviteit met services in elk deel van de wereld monitoren, vanuit een werkruimte die wordt gehost in een van de [ondersteunde regio's](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Welke regio's worden ondersteund voor de ExpressRoute Monitor van NPM?
NPM kan uw ExpressRoute-circuits in elke Azure-regio controleren. Als u aan boord wilt gaan van NPM, hebt u een Log Analytics-werkruimte nodig die moet worden gehost in een van de [ondersteunde regio's](/azure/expressroute/how-to-npm)

## <a name="troubleshoot"></a>Problemen oplossen

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Waarom zijn sommige hops gemarkeerd als niet-geïdentificeerd in de netwerktopologieweergave?
NPM gebruikt een aangepaste versie van traceroute om de topologie te ontdekken van de bronagent naar de bestemming. Een niet-geïdentificeerde hop geeft aan dat de netwerkhop niet heeft gereageerd op het verzoek om traceroute van de bronagent. Als drie opeenvolgende netwerkhops niet reageren op de traceroute van de agent, markeert de oplossing de niet-reagerende hop als niet-geïdentificeerd en probeert niet meer hop te ontdekken.

Een hop mag niet reageren op een traceroute in een of meer van de onderstaande scenario's:

* De routers zijn geconfigureerd om hun identiteit niet te onthullen.
* De netwerkapparaten staan geen ICMP_TTL_EXCEEDED verkeer toe.
* Een firewall blokkeert de ICMP_TTL_EXCEEDED reactie van het netwerkapparaat.

Wanneer een van de eindpunten in Azure ligt, wordt ongeïdentificeerde hop weergegeven omdat Azure Infrastructure geen identiteit aan traceroute onthult. 

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy"></a>Ik krijg waarschuwingen voor ongezonde tests, maar ik zie niet de hoge waarden in het verlies npm's en latentie grafiek. Hoe controleer ik wat ongezond is?
NPM geeft een waarschuwing als de latentie tussen bron en bestemming van einde tot einde de drempelwaarde voor een pad tussen deze waarden overschrijdt. Sommige netwerken hebben meerdere paden die dezelfde bron en bestemming met elkaar verbinden. NPM verhoogt een waarschuwing is een pad is ongezond. Het verlies en de latentie in de grafieken is de gemiddelde waarde voor alle paden, dus het kan niet de exacte waarde van een enkel pad weer te geven. Als u wilt weten waar de drempelwaarde is overschreden, zoekt u de kolom Subtype in de waarschuwing. Als het probleem wordt veroorzaakt door een pad, is de subtypewaarde NetworkPath (voor prestatiemonitortests), EndpointPath (voor serviceconnectiviteitsmonitortests) en ExpressRoutePath (voor ExpressRotue Monitor-tests). 

Voorbeeldquery om te zoeken is pad is niet in orde:

    NetworkMonitoring 
    | where ( SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and          CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
    | project SubType, LossHealthState, LatencyHealthState, MedianLatency 

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>Waarom toont mijn test ongezond, maar de topologie niet 
NPM bewaakt end-to-end verlies, latentie en topologie met verschillende intervallen. Verlies en latentie worden eens in de 5 seconden gemeten en elke drie minuten samengevoegd (voor Prestatiemonitor en Express Route Monitor), terwijl de topologie eenmaal per 10 minuten wordt berekend met traceroute. Tussen 3:44 en 4:04 kan de topologie bijvoorbeeld drie keer worden bijgewerkt (3:44, 3:54, 4:04), maar verlies en latentie worden ongeveer zeven keer bijgewerkt (3:44, 3:47, 3:50, 3:53, 3:56, 3:59, 4:02). De topologie gegenereerd op 3:54 zal worden weergegeven voor het verlies en latentie die wordt berekend op 3:56, 3:59 en 4:02. Stel dat u een waarschuwing krijgt dat uw ER-circuit ongezond was om 3:59. U logt in op NPM en probeert de topologietijd in te stellen op 3:59. NPM geeft de topologie weer die wordt gegenereerd om 3:54. Als u de laatst bekende topologie van uw netwerk wilt begrijpen, vergelijkt u de velden TimeProcessed (tijd waarop verlies en latentie is berekend) en TracerouteCompletedTime (tijd waarop topologie is berekend). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>Wat is het verschil tussen de velden E2EMedianLatency en AvgHopLatencyList in de tabel NetworkMonitoring
E2EMedianLatency is de latentie die elke drie minuten wordt bijgewerkt na het aggregeren van de resultaten van tcp ping-tests, terwijl AvgHopLatencyList elke 10 minuten wordt bijgewerkt op basis van traceroute. Als u wilt begrijpen op welk tijdstip E2EMedianLatency is berekend, gebruikt u het veld TimeProcessed. Als u wilt inzicht krijgen in het exacte tijdstip waarop traceroute is voltooid en bijgewerkt AvgHopLatencyList, gebruikt u het veld TracerouteCompletedTime

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Waarom verschilt hop-by-hop latentie nummers van HopLatencyValues 
HopLatencyValues zijn bron tot eindpunt.
Bijvoorbeeld: Hop - A,B,C. AvgHopLatency - 10,15,20. Dit betekent bron naar Een latentie = 10, bron naar B latentie = 15 en bron naar C latentie is 20. UI berekent A-B-hoplatentie als 5 in de topologie

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>De oplossing toont 100% verlies, maar er is connectiviteit tussen de bron en de bestemming
Dit kan gebeuren als de hostfirewall of de tussentijdse firewall (netwerkfirewall of Azure NSG) de communicatie tussen de bronagent en de bestemming over de poort die wordt gebruikt voor bewaking door NPM blokkeert (standaard is de poort 8084, tenzij de klant heeft dit veranderd).

* Als u wilt controleren of de hostfirewall de communicatie op de vereiste poort niet blokkeert, bekijkt u de status status van de bron- en doelknooppunten vanuit de volgende weergave: Network Performance Monitor -> Configuration -> Nodes. 
  Als ze ongezond zijn, bekijk dan de instructies en onderneem corrigerende maatregelen. Als de knooppunten gezond zijn, gaat u naar stap b. hieronder.
* Als u wilt controleren of een tussentijdse netwerkfirewall of Azure NSG de communicatie op de vereiste poort niet blokkeert, gebruikt u het psping-hulpprogramma van derden met behulp van de onderstaande instructies:
  * psping utility is [hier](https://technet.microsoft.com/sysinternals/psping.aspx) te downloaden 
  * Voer de volgende opdracht uit vanaf het bronknooppunt.
    * psping -n \<15 destination\>node IPAddress :portNumber Standaard maakt NPM gebruik van 8084-poort. Als u dit expliciet hebt gewijzigd met het script EnableRules.ps1, voert u het aangepaste poortnummer in dat u gebruikt). Dit is een ping van Azure-machine naar on-premises
* Controleer of de pings succesvol zijn. Als dit niet het zo is, geeft dit aan dat een tussentijdse netwerkfirewall of Azure NSG het verkeer op deze poort blokkeert.
* Voer de opdracht nu uit vanaf het doelknooppunt naar het IP-adres van het bronknooppunt.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Er is verlies van knooppunt A naar B, maar niet van knooppunt B naar A. Waarom?
Aangezien de netwerkpaden tussen A en B kunnen verschillen van de netwerkpaden tussen B en A, kunnen verschillende waarden voor verlies en latentie worden waargenomen.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Waarom worden al mijn ExpressRoute-circuits en peering-verbindingen niet ontdekt?
NPM ontdekt nu ExpressRoute-circuits en peering-verbindingen in alle abonnementen waartoe de gebruiker toegang heeft. Kies alle abonnementen waarbij uw Express Route-bronnen zijn gekoppeld en schakel bewaking in voor elke gedetecteerde bron. NPM zoekt naar verbindingsobjecten bij het ontdekken van een privépeering, dus controleer of een VNET is gekoppeld aan uw peering.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>De ER Monitor mogelijkheid heeft een diagnostisch bericht "Verkeer is niet via een circuit". Wat houdt dat in?

Er kan een scenario zijn waarin er een gezonde verbinding is tussen de on-premises en Azure-knooppunten, maar het verkeer gaat niet over het ExpressRoute-circuit dat is geconfigureerd om te worden gecontroleerd door NPM. 

Dit kan gebeuren als:

* Het ER-circuit is uitgevallen.
* De routefilters zijn zo geconfigureerd dat ze voorrang geven aan andere routes (zoals een VPN-verbinding of een ander ExpressRoute-circuit) over het beoogde ExpressRoute-circuit. 
* De on-premises en Azure-knooppunten die zijn gekozen voor het bewaken van het ExpressRoute-circuit in de bewakingsconfiguratie, hebben geen verbinding met elkaar over het beoogde ExpressRoute-circuit. Zorg ervoor dat u de juiste knooppunten hebt gekozen die verbinding met elkaar hebben over het ExpressRoute-circuit dat u wilt controleren.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>Tijdens het configureren van de bewaking van mijn ExpressRoute-circuit worden de Azure-knooppunten niet gedetecteerd.
Dit kan gebeuren als de Azure-knooppunten zijn verbonden via Operations Manager. De ExpressRoute Monitor-mogelijkheid ondersteunt alleen azure-knooppunten die zijn verbonden als Direct Agents.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Ik kan niet ontdekken via ExpressRoute circuits in het OMS portaal
Hoewel NPM zowel vanuit de Azure-portal als de OMS-portal kan worden gebruikt, werkt de circuitdetectie in de ExpressRoute Monitor-mogelijkheid alleen via de Azure-portal. Zodra de circuits zijn ontdekt via de Azure-portal, u de mogelijkheid vervolgens gebruiken in een van de twee portalen. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>In de serviceconnectivity monitor-mogelijkheid worden de serviceresponstijd, netwerkverlies en latentie weergegeven als NA
Dit kan gebeuren als een of meer waar is:

* De service is uitgevallen.
* Het knooppunt dat wordt gebruikt voor het controleren van de netwerkverbinding met de service is uitgeschakeld.
* Het doel dat in de testconfiguratie is ingevoerd, is onjuist.
* Het knooppunt heeft geen netwerkconnectiviteit.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>In de serviceconnectivity monitor-mogelijkheid wordt een geldige serviceresponstijd weergegeven, maar netwerkverlies en latentie worden weergegeven als NA
 Dit kan gebeuren als een of meer waar is:

* Als het knooppunt dat wordt gebruikt voor het controleren van de netwerkverbinding met de service een Windows-clientmachine is, blokkeert de doelservice ICMP-aanvragen of blokkeert een netwerkfirewall ICMP-verzoeken die afkomstig zijn van het knooppunt.
* Het selectievakje Netwerkmetingen uitvoeren is leeg in de testconfiguratie.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>In de serviceconnectivity monitor-mogelijkheid is de serviceresponstijd NA, maar netwerkverlies en latentie zijn geldig
Dit kan gebeuren als de doelservice geen webtoepassing is, maar de test is geconfigureerd als een webtest. Bewerk de testconfiguratie en kies het testtype als netwerk in plaats van web.

## <a name="miscellaneous"></a>Diversen

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Is er een prestatie-impact op het knooppunt dat wordt gebruikt voor monitoring?
Het NPM-proces is geconfigureerd om te stoppen als het meer dan 5% van de cpu-bronnen van de host gebruikt. Dit is om ervoor te zorgen dat u de knooppunten blijven gebruiken voor hun gebruikelijke workloads zonder dat dit gevolgen heeft voor de prestaties.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>Bewerkt NPM firewallregels voor bewaking?
NPM maakt alleen een lokale Windows Firewall-regel op de knooppunten waarop het EnableRules.ps1 Powershell-script wordt uitgevoerd, zodat de agents TCP-verbindingen met elkaar kunnen maken op de opgegeven poort. De oplossing wijzigt geen netwerkfirewall- of NSG-regels (Network Security Group).

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Hoe kan ik de status controleren van de knooppunten die worden gebruikt voor monitoring?
U de status van de knooppunten die worden gebruikt voor bewaking bekijken vanuit de volgende weergave: Network Performance Monitor -> Configuration -> Nodes. Als een knooppunt niet in orde is, u de foutgegevens bekijken en de voorgestelde actie uitvoeren.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>Kan NPM latentienummers in microseconden rapporteren?
NPM rondt de latentienummers af in de gebruikersinterface en in milliseconden. Dezelfde gegevens worden opgeslagen bij een hogere granulariteit (soms tot vier decimalen).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over netwerkprestatiemeter door te verwijzen naar [de oplossing netwerkprestatiemeter in Azure](../../azure-monitor/insights/network-performance-monitor.md).
