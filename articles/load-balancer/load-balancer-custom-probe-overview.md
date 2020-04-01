---
title: Gezondheidssondes om ha te schalen en te leveren voor uw service
titleSuffix: Azure Load Balancer
description: In dit artikel vindt u meer informatie over het gebruik van statussondes om instanties achter Azure Load Balancer te controleren
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 8e79f4c791d0252c719846da3aa8024b0e622dca
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477018"
---
# <a name="load-balancer-health-probes"></a>Status van Load Balancer testen

Wanneer u load-balancing-regels gebruikt met Azure Load Balancer, moet u statussen opgeven zodat Load Balancer de backendend-endpointstatus kan detecteren.  De configuratie van de statussonde en de sondereacties bepalen welke backendpoolexemplaren nieuwe stromen ontvangen. U statussondes gebruiken om het falen van een toepassing op een backend-eindpunt te detecteren. U ook een aangepaste reactie op een statussonde genereren en de statussonde gebruiken voor stroombeheer om belasting of geplande downtime te beheren. Wanneer een statussonde uitvalt, stopt Load Balancer met het verzenden van nieuwe stromen naar de desbetreffende ongezonde instantie. Uitgaande connectiviteit wordt niet beïnvloed, alleen inkomende connectiviteit wordt beïnvloed.

Gezondheidssondes ondersteunen meerdere protocollen. De beschikbaarheid van een specifiek protocol voor statussondes varieert per Load Balancer SKU.  Bovendien varieert het gedrag van de service per Load Balancer SKU, zoals in deze tabel wordt weergegeven:

| | Standaard SKU | Basis-SKU |
| --- | --- | --- |
| [Sondetypen](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Sonde down gedrag](#probedown) | Alle sondes omlaag, alle TCP-stromen gaan door. | Alle sondes naar beneden, alle TCP-stromen verlopen. | 


>[!IMPORTANT]
>Bekijk dit document in zijn geheel, inclusief belangrijke [ontwerprichtlijnen](#design) hieronder om een betrouwbare service te creëren.

>[!IMPORTANT]
>Load Balancer health probes zijn afkomstig van het IP-adres 168.63.129.16 en mogen niet worden geblokkeerd voor sondes om uw instantie te markeren.  Bekijk [het IP-adres van de sondebron](#probesource) voor meer informatie.

>[!IMPORTANT]
>Ongeacht de geconfigureerde time-outdrempel, zullen http(S) Load Balancer health probes automatisch een instantie sondeals de server een statuscode retourneert die niet HTTP 200 OK is of als de verbinding wordt beëindigd via TCP reset.

## <a name="probe-configuration"></a><a name="probes"></a>Sondeconfiguratie

Configuratie van de statussonde bestaat uit de volgende elementen:

- Duur van het interval tussen afzonderlijke sondes
- Aantal sondereacties dat moet worden waargenomen voordat de sonde overgaat naar een andere toestand
- Protocol van de sonde
- Poort van de sonde
- HTTP-pad te gebruiken voor HTTP GET bij het gebruik van HTTP(S) sondes

>[!NOTE]
>Een sondedefinitie is niet verplicht of gecontroleerd bij het gebruik van Azure PowerShell, Azure CLI, Templates of API. Sondevalidatietests worden alleen uitgevoerd wanneer u de Azure Portal gebruikt.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Inzicht in toepassingssignaal, detectie van het signaal en reactie van het platform

Het aantal sondereacties is van toepassing op zowel

- het aantal succesvolle sondes waarmee een instantie als up kan worden gemarkeerd, en
- het aantal getimede sondes waardoor een instantie als omlaag wordt gemarkeerd.

De opgegeven time-out- en intervalwaarden bepalen of een instantie wordt gemarkeerd als omhoog of omlaag.  De duur van het interval vermenigvuldigd met het aantal sondereacties bepaalt de duur waarin de sondereacties moeten worden gedetecteerd.  En de dienst zal reageren nadat de vereiste sondes zijn bereikt.

We kunnen het gedrag verder illustreren met een voorbeeld. Als u het aantal sondereacties hebt ingesteld op 2 en het interval op 5 seconden, betekent dit dat 2 time-outfouten van de sonde binnen een interval van 10 seconden moeten worden waargenomen.  Omdat het tijdstip waarop een sonde wordt verzonden niet wordt gesynchroniseerd wanneer uw toepassing de status kan wijzigen, kunnen we de tijd verbinden om te detecteren door twee scenario's:

1. Als uw toepassing begint met het produceren van een time-out sonde reactie net voordat de eerste sonde arriveert, zal de detectie van deze gebeurtenissen 10 seconden (2 x 5 seconden intervallen) plus de duur van de toepassing begint een time-out signaal aan wanneer de eerste sonde aankomt.  U ervan uitgaan dat deze detectie iets meer dan 10 seconden in beslag neemt.
2. Als uw toepassing begint met het produceren van een time-out sonde reactie net nadat de eerste sonde arriveert, zal de detectie van deze gebeurtenissen niet beginnen totdat de volgende sonde arriveert (en time-out) plus nog eens 10 seconden (2 x 5 seconden intervallen).  U ervan uitgaan dat deze detectie iets minder dan 15 seconden duurt.

In dit voorbeeld neemt het platform, zodra detectie heeft plaatsgevonden, een kleine hoeveelheid tijd om op deze wijziging te reageren.  Dit betekent een afhankelijk van 

1. wanneer de toepassing begint de status te wijzigen en
2. wanneer deze wijziging wordt gedetecteerd en voldeed aan de vereiste criteria (aantal sondes die met het opgegeven interval worden verzonden) en
3. wanneer de detectie over het platform is gecommuniceerd 

u ervan uitgaan dat de reactie op een time-out sonderespons tussen een minimum van iets meer dan 10 seconden en een maximum van iets meer dan 15 seconden zal duren om te reageren op een wijziging in het signaal van de toepassing.  Dit voorbeeld wordt gegeven om te illustreren wat er gebeurt, maar het is niet mogelijk om een exacte duur te voorspellen die verder gaat dan de bovenstaande ruwe richtsnoeren die in dit voorbeeld worden geïllustreerd.

>[!NOTE]
>De statussonde sonde zal alle lopende instanties in de backend pool onderzoeken. Als een instantie wordt gestopt, wordt deze pas gesondeerd als deze opnieuw is gestart.

## <a name="probe-types"></a><a name="types"></a>Sondetypen

Het protocol dat door de statussonde wordt gebruikt, kan worden geconfigureerd tot een van de volgende opties:

- [TCP-listeners](#tcpprobe)
- [HTTP-eindpunten](#httpprobe)
- [HTTPS-eindpunten](#httpsprobe)

De beschikbare protocollen zijn afhankelijk van de gebruikte SKU load Balancer:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standaard SKU |    &#9989; |   &#9989; |   &#9989; |
| Basis-SKU |   &#9989; |   &#9989; | &#10060; |

### <a name="tcp-probe"></a><a name="tcpprobe"></a>TCP-sonde

TCP-sondes starten een verbinding door een drierichtingsopen TCP-handshake uit te voeren met de gedefinieerde poort.  TCP-sondes beëindigen een verbinding met een vierwegdichte TCP-handshake.

Het minimale sondeinterval is 5 seconden en het minimum aantal ongezonde reacties is 2.  De totale duur van alle intervallen mag niet langer zijn dan 120 seconden.

Een TCP-sonde mislukt wanneer:
* De TCP-listener in de instantie reageert helemaal niet tijdens de time-outperiode.  Een sonde wordt gemarkeerd op basis van het aantal time-out sonde aanvragen, die zijn geconfigureerd om onbeantwoord te blijven voordat markering van de sonde.
* De sonde ontvangt een TCP-reset van de instantie.

Het volgende illustreert hoe u dit soort sondeconfiguratie uitdrukken in een resourcemanagersjabloon:

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="http--https-probe"></a><a name="httpprobe"></a><a name="httpsprobe"></a> HTTP / HTTPS-sonde

>[!NOTE]
>HTTPS-sonde is alleen beschikbaar voor [Standard Load Balancer](load-balancer-standard-overview.md).

HTTP- en HTTPS-sondes bouwen voort op de TCP-sonde en geven een HTTP GET uit met het opgegeven pad. Beide sondes ondersteunen relatieve paden voor de HTTP GET. HTTPS-sondes zijn hetzelfde als HTTP-sondes met de toevoeging van een Transport Layer Security (TLS, voorheen bekend als SSL) wrapper. De statussonde wordt gemarkeerd wanneer de instantie binnen de time-outperiode reageert met een HTTP-status 200.  De statussonde probeert standaard elke 15 seconden de geconfigureerde statussondepoort te controleren. Het minimale sondeinterval is 5 seconden. De totale duur van alle intervallen mag niet langer zijn dan 120 seconden.

HTTP / HTTPS-sondes kunnen ook handig zijn om uw eigen logica te implementeren om exemplaren uit de rotatie van de load balancer te verwijderen als de sondepoort ook de listener voor de service zelf is. U bijvoorbeeld besluiten een instantie te verwijderen als deze boven de 90% CPU ligt en een niet-200 HTTP-status retourneert. 

> [!NOTE] 
> De HTTPS Probe vereist het gebruik van certificaten gebaseerd die een minimale handtekening hash van SHA256 in de hele keten hebben.

Als u Cloud Services gebruikt en webrollen hebt die w3wp.exe gebruiken, bereikt u ook automatische bewaking van uw website. Fouten in uw websitecode geven een niet-200-status terug naar de sonde van de load balancer.

Een HTTP / HTTPS-sonde mislukt wanneer:
* Eindpunt van de sonde retourneert een andere HTTP-antwoordcode dan 200 (bijvoorbeeld 403, 404 of 500). Dit zal de gezondheidssonde onmiddellijk markeren. 
* Het eindpunt van de sonde reageert helemaal niet tijdens het minimum van het sondeinterval en de time-outperiode van 30 seconden. Meerdere sondeaanvragen kunnen onbeantwoord blijven voordat de sonde wordt gemarkeerd als niet-uitgevoerd en totdat de som van alle time-outintervallen is bereikt.
* Probe endpoint sluit de verbinding via een TCP reset.

Het volgende illustreert hoe u dit soort sondeconfiguratie uitdrukken in een resourcemanagersjabloon:

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guest-agent-probe-classic-only"></a><a name="guestagent"></a>Gastagent sonde (alleen Klassiek)

Cloudservicerollen (werknemersrollen en webrollen) gebruiken standaard een gastagent voor sondebewaking.  Een gastagent sonde is een laatste redmiddel configuratie.  Gebruik altijd een statussonde expliciet met een TCP- of HTTP-sonde. Een gastagentsonde is niet zo effectief als expliciet gedefinieerde sondes voor de meeste toepassingsscenario's.

Een gastagent sonde is een controle van de gastagent binnen vm. Vervolgens wordt er alleen naar geluisterd en gereageerd met een HTTP 200 OK-antwoord wanneer de instantie in de status Klaar staat. (Andere staten zijn bezet, recycling of stoppen.)

Zie [Het servicedefinitiebestand (csdef) configureren voor statussondes](https://msdn.microsoft.com/library/azure/ee758710.aspx) of [Aan de slag door een public load balancer voor cloudservices te maken](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services)voor meer informatie.

Als de gastagent niet reageert met HTTP 200 OK, markeert de load balancer de instantie als niet-reagerend. Het stopt dan met het verzenden van stromen naar die instantie. De load balancer blijft de instantie controleren. 

Als de gastagent reageert met een HTTP 200, stuurt de load balancer opnieuw nieuwe stromen naar die instantie.

Wanneer u een webrol gebruikt, wordt de websitecode meestal uitgevoerd in w3wp.exe, dat niet wordt gecontroleerd door de Azure-fabric of gastagent. Fouten in w3wp.exe (bijvoorbeeld HTTP 500-antwoorden) worden niet gemeld aan de gastagent. Bijgevolg neemt de load balancer die instantie niet uit de rotatie.

<a name="health"></a>
## <a name="probe-up-behavior"></a><a name="probehealth"></a>Sonde gedrag

TCP-, HTTP- en HTTPS-statussondes worden als gezond beschouwd en markeren het backend-eindpunt als gezond wanneer:

* De health probe is eenmaal succesvol na de VM-laarzen.
* Het opgegeven aantal sondes dat nodig is om het backend-eindpunt als gezond te markeren, is bereikt.

Elk backend endpoint dat een gezonde status heeft bereikt, komt in aanmerking voor het ontvangen van nieuwe stromen.  

> [!NOTE]
> Als de statussonde fluctueert, wacht de load balancer langer voordat het backend endpoint weer in de gezonde staat wordt gebracht. Deze extra wachttijd beschermt de gebruiker en de infrastructuur en is een opzettelijk beleid.

## <a name="probe-down-behavior"></a><a name="probedown"></a>Sonde down gedrag

### <a name="tcp-connections"></a>TCP-verbindingen

Nieuwe TCP-verbindingen slagen erin om een gezond backend endpoint te blijven.

Als de statusssonde van een backend-endpoint mislukt, worden de gevestigde TCP-verbindingen met dit backend-eindpunt voortgezet.

Als alle sondes voor alle instanties in een backendpool mislukken, worden er geen nieuwe stromen naar de backendpool verzonden. Met Standard Load Balancer kunnen gevestigde TCP-stromen worden voortgezet.  Basic Load Balancer beëindigt alle bestaande TCP-stromen naar de backendpool.
 
Load Balancer is een pass through-service (beëindigt geen TCP-verbindingen) en de stroom is altijd tussen de client en het gastbesturingssysteem en de toepassing van de VM. Een pool met alle sondes omlaag zal leiden tot een frontend om niet te reageren op TCP-verbinding open pogingen (SYN) omdat er geen gezonde backend endpoint is om de stroom te ontvangen en te reageren met een SYN-ACK.

### <a name="udp-datagrams"></a>UDP-datagrammen

UDP datagrammen worden geleverd aan gezonde backend endpoints.

UDP is verbindingsloos en er is geen stroomstatus bijgehouden voor UDP. Als de statusssonde van backendendpoint mislukt, worden bestaande UDP-stromen verplaatst naar een andere gezonde instantie in de backendpool.

Als alle sondes voor alle instanties in een backendpool mislukken, worden bestaande UDP-stromen beëindigd voor Basis- en Standaardtaakbalansen.

<a name="source"></a>
## <a name="probe-source-ip-address"></a><a name="probesource"></a>IP-adres van probebron

Load Balancer maakt gebruik van een gedistribueerde indringende service voor zijn interne gezondheidsmodel. De indringende service bevindt zich op elke host waar VM's en kan on-demand worden geprogrammeerd om statussondes per configuratie van de klant te genereren. Het verkeer van de statussonde bevindt zich rechtstreeks tussen de indringende service die de statussonde genereert en de VM van de klant. Alle load balancer health probes zijn afkomstig van het IP-adres 168.63.129.16 als bron.  U IP-adresruimte gebruiken in een VNet dat geen RFC1918-ruimte is.  Met behulp van een wereldwijd gereserveerd IP-adres dat eigendom is van Microsoft, verkleint de kans op een IP-adresconflict met de IP-adresruimte die u in het VNet gebruikt.  Dit IP-adres is in alle regio's hetzelfde en verandert niet en vormt geen beveiligingsrisico omdat alleen de interne Azure-platformcomponent een pakket kan bron van dit IP-adres. 

De AzureLoadBalancer-servicetag identificeert dit bron-IP-adres in uw [netwerkbeveiligingsgroepen](../virtual-network/security-overview.md) en maakt standaard statussondeverkeer mogelijk.

Naast load Balancer health probes gebruiken de [volgende bewerkingen dit IP-adres:](../virtual-network/what-is-ip-address-168-63-129-16.md)

- Hiermee kan de VM-agent communiceren met het platform om te signaleren dat het in een "Ready"-status is
- Hiermee kan de communicatie met de virtuele DNS-server gefilterde naamomzetting bieden aan klanten die geen aangepaste DNS-servers definiëren.  Deze filtering zorgt ervoor dat klanten alleen de hostnamen van hun implementatie kunnen oplossen.
- Hiermee kan de VM een dynamisch IP-adres verkrijgen van de DHCP-service in Azure.

## <a name="design-guidance"></a><a name="design"></a>Ontwerprichtlijnen

Health probes worden gebruikt om uw service veerkrachtig te maken en te laten schalen. Een verkeerde configuratie of een slecht ontwerppatroon kan van invloed zijn op de beschikbaarheid en schaalbaarheid van uw service. Bekijk dit hele document en bedenk wat de impact is op uw scenario wanneer deze sonderespons is gemarkeerd of gemarkeerd en hoe dit van invloed is op de beschikbaarheid van uw toepassingsscenario.

Wanneer u het statusmodel voor uw toepassing ontwerpt, moet u een poort op een backend-endpoint onderzoeken die de status van die instantie __en__ de toepassingsservice die u aanbiedt, weerspiegelt.  De toepassingspoort en de sondepoort hoeven niet hetzelfde te zijn.  In sommige scenario's kan het wenselijk zijn dat de sondepoort anders is dan de poort waarop uw toepassing service biedt.  

Soms kan het handig zijn voor uw toepassing om een reactie op de status van een status van een status van een status van een status van uw toepassing te genereren, maar ook rechtstreeks naar Load Balancer te sturen of uw instantie nieuwe stromen moet ontvangen of niet ontvangt.  U de sonderespons manipuleren zodat uw toepassing backdruk en gashendellevering van nieuwe stromen naar een instantie kan maken door de statussonde te mislukken of u voor te bereiden op het onderhoud van uw toepassing en uw scenario te starten.  Bij het gebruik van Standard Load Balancer zorgt een [sonde-downsignaal](#probedown) ervoor dat TCP-stromen altijd kunnen worden voortgezet totdat er geen time-out of verbindingssluiting is. 

Voor UDP-taakverdeling moet u een aangepast statussondessignaalsignaal genereren vanaf het backend-eindpunt en een TCP-, HTTP- of HTTPS-statussonde gebruiken die zich richt op de bijbehorende listener om de status van uw UDP-toepassing weer te geven.

Bij het gebruik van [HA-poorten load-balancing regels](load-balancer-ha-ports-overview.md) met [Standard Load Balancer,](load-balancer-standard-overview.md)alle poorten zijn load balanced en een enkele health probe reactie moet de status van de gehele instantie weerspiegelen.

Vertaal of proxy een statussonde niet door de instantie die de statussonde ontvangt naar een andere instantie in uw VNet, omdat deze configuratie kan leiden tot trapsgewijze fouten in uw scenario.  Denk aan het volgende scenario: een set apparaten van derden wordt geïmplementeerd in de backend-bron van een Load Balancer-bron om schaal en redundantie voor de apparaten te bieden en de statussonde is geconfigureerd om een poort te onderzoeken die de proxy's van het apparaat van derden of vertaalt naar andere virtuele machines achter het apparaat.  Als u dezelfde poort gebruikt die u gebruikt om aanvragen of proxy-aanvragen te vertalen naar de andere virtuele machines achter het apparaat, zal elke sonderespons van één virtuele machine achter het apparaat het apparaat zelf dood markeren. Deze configuratie kan leiden tot een trapsgewijze storing van het hele toepassingsscenario als gevolg van één backend endpoint achter het toestel.  De trigger kan een onderbroken sondestoring zijn waardoor Load Balancer de oorspronkelijke bestemming (de toestelinstantie) wordt afgeschreven en op zijn beurt het hele toepassingsscenario kan worden uitgeschakeld. Sonde in plaats daarvan de status van het apparaat zelf. De selectie van de sonde om het gezondheidssignaal te bepalen is een belangrijke overweging voor netwerk virtuele apparaten (NVA) scenario's en u moet uw toepassing leverancier te raadplegen voor wat het juiste gezondheidssignaal is voor dergelijke scenario's.

Als u het [bron-IP](#probesource) van de sonde niet toestaat in uw firewallbeleid, mislukt de statussonde omdat deze uw instantie niet kan bereiken.  Load Balancer markeert op zijn beurt uw instantie als gevolg van het falen van de statussonde.  Deze verkeerde configuratie kan ertoe leiden dat het scenario van uw load balanced application mislukt.

Als u de statusstest van Load Balancer wilt markeren, **moet** u dit IP-adres toestaan in [azure-netwerkbeveiligingsgroepen](../virtual-network/security-overview.md) en lokaal firewallbeleid.  Standaard bevat elke netwerkbeveiligingsgroep de [servicetag](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer om het verkeer van statussondes toe te staan.

Als u een fout in een statussonde wilt testen of een afzonderlijke instantie wilt markeren, u een [netwerkbeveiligingsgroepen](../virtual-network/security-overview.md) gebruiken om de statussonde (doelpoort of [bron-IP)](#probesource)expliciet te blokkeren en het falen van een sonde te simuleren.

Configureer uw VNet niet met het IP-adresbereik dat eigendom is van Microsoft en dat 168.63.129.16 bevat.  Dergelijke configuraties botsen met het IP-adres van de statussonde en kunnen ervoor zorgen dat uw scenario mislukt.

Als u meerdere interfaces op uw VM hebt, moet u ervoor zorgen dat u reageert op de sonde op de interface waarop u deze hebt ontvangen.  Mogelijk moet u dit adres per interface in de VM vertalen.

Schakel [tcp-tijdstempels](https://tools.ietf.org/html/rfc1323)niet in.  Als u TCP-tijdstempels inschakelt, kunnen statussen mislukken omdat TCP-pakketten worden verwijderd door de TCP-stack van het gastbesturingssysteem van de VM, waardoor Load Balancer het desbetreffende eindpunt markeert.  TCP-tijdstempels worden standaard ingeschakeld op geharde VM-afbeeldingen en moeten worden uitgeschakeld.

## <a name="monitoring"></a>Bewaking

Zowel openbare als interne [Standard Load Balancer](load-balancer-standard-overview.md) worden per eindpunt en backend endpoint status van eindpunten weergegeven als multidimensionale statistieken via Azure Monitor. Deze statistieken kunnen worden verbruikt door andere Azure-services of partnertoepassingen. 

Basic public Load Balancer legt status van status van status samengevat per backend pool bloot via Azure Monitor-logboeken.  Azure Monitor-logboeken zijn niet beschikbaar voor interne basislastsbalansen.  U [Azure Monitor-logboeken](load-balancer-monitor-log.md) gebruiken om de status van de status van de sonde en het aantal sondesen te controleren. Logboekregistratie kan worden gebruikt met Power BI of Azure Operational Insights om statistieken te geven over de status van load balancer.

## <a name="limitations"></a>Beperkingen

- HTTPS-sondes ondersteunen geen wederzijdse verificatie met een clientcertificaat.
- U moet ervan uitgaan dat statussondes mislukken wanneer TCP-tijdstempels zijn ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Standard Load Balancer](load-balancer-standard-overview.md)
- [Aan de slag met het maken van een public load balancer in Resource Manager met PowerShell](quickstart-create-standard-load-balancer-powershell.md)
- [REST API voor health probes](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Vraag nieuwe health probe-vaardigheden aan met [Load Balancer's Uservoice](https://aka.ms/lbuservoice)
