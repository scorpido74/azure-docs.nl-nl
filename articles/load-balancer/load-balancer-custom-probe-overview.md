---
title: Gebruik Azure Load Balancer status tests om te schalen en hoge Beschik baarheid te bieden voor uw service
titlesuffix: Azure Load Balancer
description: Informatie over het gebruik van statuscontroles instanties achter Load Balancer bewaken
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: d9873c4d8d4ca2b74532706f079c7384bf38db42
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033825"
---
# <a name="load-balancer-health-probes"></a>Status van Load Balancer testen

Wanneer u taakverdelings regels met Azure Load Balancer gebruikt, moet u een status test opgeven zodat Load Balancer de status van het back-end-eind punt kan detecteren.  De configuratie van de status test en test reacties bepalen welke back-endadresgroep instanties nieuwe stromen ontvangen. U kunt Health tests gebruiken om het mislukken van een toepassing op een back-end-eind punt te detecteren. U kunt ook een aangepast antwoord op een status test genereren en de status test voor datatransport besturing gebruiken om de belasting of geplande downtime te beheren. Wanneer een status test is mislukt, verzendt Load Balancer geen nieuwe stromen meer naar het respectieve beschadigde exemplaar.

Status controles bieden ondersteuning voor meerdere protocollen. De beschik baarheid van een specifiek Health probe protocol verschilt per Load Balancer SKU.  Daarnaast is het gedrag van de service afhankelijk van de SKU van Load Balancer, zoals wordt weer gegeven in deze tabel:

| | Standaard SKU | Basis-SKU |
| --- | --- | --- |
| [Test-typen](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Test omlaag gedrag](#probedown) | Alle tests, gaan alle TCP-stromen. | Alle tests worden uitgeschakeld, alle TCP-stromen verlopen. | 


>[!IMPORTANT]
>Bekijk dit document in zijn geheel, met inbegrip van belang rijke [ontwerp richtlijnen](#design) hieronder om een betrouw bare service te maken.

>[!IMPORTANT]
>Load Balancer status tests zijn afkomstig van het IP-adres 168.63.129.16 en mogen niet worden geblokkeerd voor tests om uw exemplaar te markeren.  Beoordeling [bron-IP-adres-test](#probesource) voor meer informatie.

## <a name="probes"></a>Configuratie testen

De status test configuratie bestaat uit de volgende elementen:

- De duur van het interval tussen afzonderlijke tests
- Aantal test reacties dat moet worden waargenomen voordat de test overgaat naar een andere status
- Protocol van de test
- Poort van de test
- Het HTTP-pad dat moet worden gebruikt voor HTTP ophalen wanneer HTTP (S)-tests worden gebruikt

> [!NOTE]
> Een test definitie is niet verplicht of wordt gecontroleerd op het gebruik van Azure PowerShell, Azure CLI, sjablonen of API. Test validatie tests worden alleen uitgevoerd wanneer de Azure-portal wordt gebruikt.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Informatie over het toepassings signaal, de detectie van het signaal en de reactie van het platform

Het aantal test reacties is van toepassing op beide

- het aantal geslaagde tests waarmee een exemplaar kan worden gemarkeerd als actief, en
- het aantal mislukte tests dat ervoor zorgt dat een exemplaar wordt gemarkeerd als niet-actief.

De opgegeven time-outwaarde en interval waarden bepalen of een exemplaar wordt gemarkeerd als up of down.  De duur van het interval vermenigvuldigd met het aantal test reacties bepaalt de duur waarin de test reacties moeten worden gedetecteerd.  De service reageert pas nadat de vereiste tests zijn uitgevoerd.

We kunnen het gedrag verder met een voor beeld illustreren. Als u het aantal test reacties op 2 en het interval tot vijf seconden hebt ingesteld, betekent dit dat 2 test fouten binnen een interval van 10 seconden moeten worden waargenomen.  Omdat het tijdstip waarop een test wordt verzonden, niet is gesynchroniseerd wanneer uw toepassing de status kan wijzigen, kunnen we de tijd voor het detecteren van twee scenario's binden:

1. Als uw toepassing begint met het produceren van een mislukte test respons voordat de eerste test arriveert, duurt het detecteren van deze gebeurtenissen tien seconden (2 x 5 seconde intervallen), plus de duur van de toepassing die begint met het melden van een fout wanneer de eerste Probe arriveert.  U kunt ervan uitgaan dat deze detectie iets langer duurt dan tien seconden.
2. Als uw toepassing begint met het produceren van een mislukte test reactie nadat de eerste test is binnengekomen, wordt de detectie van deze gebeurtenissen pas gestart wanneer de volgende probe arriveert (en mislukt) plus een andere 10 seconden (2 x 5 seconden intervallen).  U kunt ervan uitgaan dat deze detectie net minder dan 15 seconden duurt.

Voor dit voor beeld neemt het platform een korte tijd in beslag om te reageren op deze wijziging.  Dit betekent een afhankelijk van 

1. Wanneer de toepassing begint met het wijzigen van de status en
2. Wanneer deze wijziging wordt gedetecteerd en voldoet aan de vereiste criteria (aantal tests die zijn verzonden met het opgegeven interval) en
3. Wanneer de detectie op het platform is gecommuniceerd 

u kunt aannemen dat de reactie van een mislukte test wordt uitgevoerd tussen mini maal 10 seconden en een maximum van minder dan 15 seconden om te reageren op een wijziging in het signaal van de toepassing.  Dit voor beeld wordt gegeven om te illustreren wat er gebeurt, maar het is niet mogelijk om een exacte duur te ramen dan de hierboven aangegeven algemene richt lijnen die in dit voor beeld worden geïllustreerd.
 
## <a name="types"></a>Test-typen

Het protocol dat wordt gebruikt door de status test kan worden geconfigureerd met een van de volgende:

- [TCP-listeners](#tcpprobe)
- [HTTP-eindpunten](#httpprobe)
- [HTTPS-eindpunten](#httpsprobe)

De beschik bare protocollen zijn afhankelijk van de gebruikte Load Balancer SKU:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standaard SKU |    &#9989; |   &#9989; |   &#9989; |
| Basis-SKU |   &#9989; |   &#9989; | &#10060; |

### <a name="tcpprobe"></a> TCP-test

TCP-tests opnieuw verbinding maken met het uitvoeren van een drie richtingen open TCP-handshake met de gedefinieerde poort.  TCP-tests beëindigen een verbinding met een in vier richtingen gesloten TCP-handshake.

De minimale testinterval is 5 seconden en het minimale aantal antwoorden niet in orde is 2.  De totale duur van alle intervallen mag niet langer zijn dan 120 seconden.

Een TCP-test mislukt wanneer:
* De TCP listener op het exemplaar reageert helemaal niet tijdens de time-outperiode.  Een test wordt gemarkeerd op basis van het aantal mislukte test aanvragen dat is geconfigureerd om te worden geantwoord voordat de test wordt ingedrukt.
* De test ontvangt een TCP opnieuw instellen van het exemplaar.

Hieronder ziet u hoe u dit type test configuratie kunt uitdrukken in een resource manager-sjabloon:

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

### <a name="httpprobe"></a> <a name="httpsprobe"></a> HTTP / HTTPS-test

>[!NOTE]
>HTTPS-test is alleen beschikbaar voor [Standard Load Balancer](load-balancer-standard-overview.md).

HTTP-en HTTPS-tests bouwen op de TCP-test en geven een HTTP-GET met het opgegeven pad. Beide van deze tests ondersteuning voor relatieve paden voor de HTTP GET. HTTPS-tests zijn hetzelfde als HTTP-tests met de toevoeging van een Transport Layer Security (TLS, voorheen bekend als SSL) wrapper. De statustest is gemarkeerd als het exemplaar met een HTTP-statuscode 200 binnen de time-outperiode reageert.  Met de status test wordt standaard elke 15 seconden geprobeerd om de geconfigureerde poort voor Health probe te controleren. De minimale testinterval is 5 seconden. De totale duur van alle intervallen mag niet langer zijn dan 120 seconden.

HTTP/HTTPS-tests kunnen ook handig zijn om uw eigen logica te implementeren om exemplaren te verwijderen uit load balancer draaiing als de test poort ook de listener voor de service zelf is. Bijvoorbeeld, kan u wilt verwijderen van een exemplaar als deze hoger dan 90% CPU is en een niet - 200 HTTP-status retourneren. 

Als u gebruik van Cloudservices en web-functies die gebruikmaken van w3wp.exe hebben, bereikt u ook automatische bewaking van uw website. De status van een niet-200 terug fouten in de websitecode van uw naar de load balancer-test.

Een HTTP / HTTPS-test mislukt wanneer:
* Statustest-eindpunt retourneert een HTTP-responscode dan 200 (bijvoorbeeld, 403, 404 of 500). Hiermee wordt de status test onmiddellijk gemarkeerd. 
* Het test eindpunt reageert helemaal niet op de time-outperiode van 31 seconden. Meerdere test aanvragen worden mogelijk niet beantwoord voordat de test wordt gemarkeerd als niet-actief en totdat de som van alle time-outintervals is bereikt.
* De verbinding via een TCP-opnieuw instellen van statustest-eindpunt wordt gesloten.

Hieronder ziet u hoe u dit type test configuratie kunt uitdrukken in een resource manager-sjabloon:

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

### <a name="guestagent"></a>Test van Gast-agent (alleen klassiek)

Cloudservicerollen (werkrollen en webrollen) gebruiken een gastagent voor het bewaken van test standaard.  Een gast agent test is een laatste redmiddel-configuratie.  Gebruik altijd expliciet een status test met een TCP-of HTTP-test. De test voor een gast-agent is niet zo effectief expliciet gedefinieerde tests voor de meeste scenario's van toepassing.

De test voor een gast-agent is een controle van de gastagent binnen de virtuele machine. Vervolgens wordt geluisterd en reageert met een HTTP 200 OK antwoord alleen wanneer het exemplaar in de status gereed heeft is. (Overige statussen zijn bezet, Recycling of stoppen.)

Zie voor meer informatie, [het servicedefinitiebestand (csdef) configureren voor statuscontroles](https://msdn.microsoft.com/library/azure/ee758710.aspx) of [aan de slag met het maken van een openbare load balancer voor cloudservices](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services).

Als de guest-agent niet reageert met een HTTP 200 OK, markeert de load balancer het exemplaar als reageert niet. Wordt geblokkeerd stromen te verzenden naar dat exemplaar. De load balancer blijft om te controleren of het exemplaar. 

Als de guest-agent met een HTTP-200 reageert, stuurt de load balancer nieuwe stromen opnieuw naar dat exemplaar.

Wanneer u een Webrol, de websitecode meestal wordt uitgevoerd in w3wp.exe, die niet wordt bewaakt door de Azure-infrastructuur of Gast-agent. Fouten in w3wp.exe (bijvoorbeeld 500 HTTP-antwoorden) worden niet gerapporteerd aan de gastagent. Als gevolg daarvan kan neemt de load balancer dat exemplaar uitgeschakeld.

<a name="health"></a>
## <a name="probehealth"></a>Gedrag van testen

TCP-, HTTP-en HTTPS-status tests worden als gezond beschouwd en markeren als in orde wanneer:

* De status test is na het opstarten van de VM geslaagd.
* Het opgegeven aantal tests dat is vereist om het back-end-eind punt te markeren als in orde is bereikt.

Elk back-end-eind punt dat een goede status heeft bereikt, komt in aanmerking voor het ontvangen van nieuwe stromen.  

> [!NOTE]
> Als de status test schommelt, wordt de load balancer opnieuw gewacht voordat het back-end-eind punt weer in de status in orde wordt gezet. Deze extra wachttijd beschermt de gebruiker en de infrastructuur en is een opzettelijke beleid.

## <a name="probedown"></a>Test omlaag gedrag

### <a name="tcp-connections"></a>TCP-verbindingen

Nieuwe TCP-verbindingen kunnen het resterende back-end van een ongeluk hebben.

Als de status test van een back-end-eind punt mislukt, worden er TCP-verbindingen met dit back-end-eind punt gemaakt.

Als alle tests voor alle exemplaren in een back-endpool mislukken, worden er geen nieuwe stromen worden verzonden naar de back-endpool. Standard Load Balancer toestaat tot stand gebrachte TCP stromen om door te gaan.  Basic Load Balancer wordt beëindigd als alle TCP-stromen naar de back-endpool.
 
Load Balancer is een Pass Through-service (er worden geen TCP-verbindingen beëindigd) en de stroom is altijd tussen de client en het gast besturingssysteem en de toepassing van de virtuele machine. Een groep met alle tests zorgt ervoor dat een frontend niet reageert op de TCP-verbindings pogingen (SYN) omdat er geen gezonde back-end-eind punt is om de stroom te ontvangen en te reageren met een SYN-ACK.

### <a name="udp-datagrams"></a>UDP-datagrammen

UDP-data grammen worden afgeleverd aan goede back-end-eind punten.

UDP is zonder verbinding en er is geen flow-status voor UDP bijgehouden. Als de status test van een back-end-eind punt mislukt, kunnen de bestaande UDP-stromen worden verplaatst naar een ander ongezonde exemplaar in de back-end-pool.

Als alle tests voor alle exemplaren in een back-endpool mislukken, worden bestaande UDP-stromen worden beëindigd voor Basic en Standard Load Balancers.

<a name="source"></a>
## <a name="probesource"></a>Bron-IP-adres-test

Load Balancer maakt gebruik van een gedistribueerde testinterval service voor de interne statusmodel. De probing-service bevindt zich op elke host waarop Vm's en op aanvraag kunnen worden geprogrammeerd om status controles te genereren volgens de configuratie van de klant. Het status test verkeer is direct tussen de probing-service die de status test en de virtuele machine van de klant genereert. Alle tests van de Load Balancer zijn afkomstig uit het IP-adres 168.63.129.16 als bron gebruikt.  U kunt IP-adres ruimte gebruiken binnen een VNet dat geen RFC1918 ruimte heeft.  Met een wereld wijd gereserveerd IP-adres van micro soft verkleint u de kans op een IP-adres conflict met de IP-adres ruimte die u in het VNet gebruikt.  Dit IP-adres is hetzelfde in alle regio's en wordt niet gewijzigd en vormt geen beveiligings risico omdat alleen het interne onderdeel van het Azure-platform een pakket kan bron van dit IP-adres. 

Het AzureLoadBalancer-service label identificeert dit bron-IP-adres in uw [netwerk beveiligings groepen](../virtual-network/security-overview.md) en staat standaard het verkeer van de status test toe.

Naast Load Balancer status tests [gebruiken de volgende bewerkingen dit IP-adres](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Kan de VM-Agent om te communiceren met het platform om aan te geven is in een status 'Gereed'
- Communicatie met de virtuele DNS-server voor gefilterde naamomzetting voor klanten die geen aangepaste DNS-servers definieert mogelijk maakt.  Dit filter zorgt ervoor dat klanten alleen de hostnamen van de implementatie kunnen omzetten.
- Hiermee kan de virtuele machine een dynamisch IP-adres verkrijgen van de DHCP-service in Azure.

## <a name="design"></a>Ontwerp richtlijnen

Status tests worden gebruikt om uw service robuust te maken en te laten schalen. Een onjuiste configuratie of een onjuist ontwerp patroon kan invloed hebben op de beschik baarheid en schaal baarheid van uw service. Bekijk dit hele document en bedenk wat het effect van uw scenario is wanneer deze test reactie wordt gemarkeerd als niet-actief of gemarkeerd, en hoe deze invloed heeft op de beschik baarheid van uw toepassings scenario.

Wanneer u het status model voor uw toepassing ontwerpt, moet u een poort op een back-end-eind punt testen die de status van die instantie __en__ de toepassings service die u levert, weergeeft.  De poort van de toepassing en de test poort hoeven niet hetzelfde te zijn.  In sommige gevallen kan het wenselijk zijn dat de test poort afwijkt van de poort waarop uw toepassing service levert.  

Soms kan het nuttig zijn dat uw toepassing een reactie van een status test genereert om de status van uw toepassing niet alleen te detecteren, maar u kunt ook rechtstreeks een signaal Load Balancer of uw instantie nieuwe stromen moet ontvangen of ontvangen.  U kunt het test antwoord bewerken zodat uw toepassing de levering van de nieuwe stromen naar een exemplaar kan maken en de bezorging kan vertragen door de status te controleren of door een storing in te stellen voor het onderhoud van uw toepassing.  Wanneer u Standard Load Balancer gebruikt, wordt TCP-stromen altijd door een [test](#probedown) signaal toegestaan totdat de time-out voor inactiviteit of het sluiten van de verbinding is verbroken. 

Voor UDP-taak verdeling moet u een aangepast Health probe-signaal genereren op basis van het back-end-eind punt en ofwel een TCP-, HTTP-of HTTPS-status test gebruiken als doel voor de bijbehorende listener om de status van uw UDP-toepassing weer te geven.

Bij gebruik van de taakverdelings [regels voor ha-poorten](load-balancer-ha-ports-overview.md) met [Standard Load Balancer](load-balancer-standard-overview.md)worden alle poorten gelijkmatig verdeeld en de status van het hele exemplaar moet overeenkomen met een enkel antwoord voor de status test.

Vertaal of proxy een status test niet via het exemplaar dat de status test naar een ander exemplaar in uw VNet ontvangt, aangezien deze configuratie kan leiden tot het trapsgewijs uitvallen van fouten in uw scenario.  Bekijk het volgende scenario: een set apparaten van derden wordt geïmplementeerd in de back-endadresgroep van een Load Balancer bron om schaal en redundantie te bieden voor de apparaten en de status test is geconfigureerd om een poort te testen die de apparaat-proxy's van derden of vertaalt op andere virtuele machines achter het apparaat.  Als u de poort die u gebruikt om te vertalen of proxy aanvragen van de andere virtuele machines achter het apparaat, wilt testen, zal elke test reactie van één virtuele machine achter het apparaat het apparaat zelf markeren als inactief. Deze configuratie kan leiden tot een trapsgewijze uitval van het hele toepassings scenario als gevolg van één back-end-eind punt achter het apparaat.  De trigger kan een onregelmatige test fout veroorzaken, waardoor Load Balancer de oorspronkelijke bestemming (de instantie van het apparaat) aanduidt en op zijn beurt uw volledige toepassings scenario kunt uitschakelen. Test in plaats daarvan de status van het apparaat zelf. De selectie van de test om het status signaal te bepalen is een belang rijke overweging voor NVA-scenario's (Network Virtual Appliance) en u moet de leverancier van uw toepassing raadplegen voor wat het juiste gezondheids signaal is voor dergelijke scenario's.

Als u het [bron-IP-adres](#probesource) van de test in uw firewall beleid niet toestaat, zal de status test mislukken omdat het niet mogelijk is om uw exemplaar te bereiken.  Load Balancer wordt op zijn beurt markeren in uw exemplaar omdat de status test is mislukt.  Deze onjuiste configuratie kan ertoe leiden dat het toepassings scenario met taak verdeling mislukt.

Voor de status test van Load Balancer om uw exemplaar te markeren, **moet** u dit IP-adres toestaan in alle Azure- [netwerk beveiligings groepen](../virtual-network/security-overview.md) en lokaal firewall beleid.  Standaard bevat elke netwerk beveiligings groep de [service label](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer om verkeer met de status test toe te staan.

Als u een mislukte status test wilt testen of een afzonderlijk exemplaar wilt markeren, kunt u een [netwerk beveiligings groepen](../virtual-network/security-overview.md) gebruiken om de status test (doel poort of [bron-IP](#probesource)) expliciet te blok keren en de fout van een test te simuleren.

Configureer uw VNet niet met het IP-adres bereik van micro soft dat 168.63.129.16 bevat.  Dergelijke configuraties conflicteren met het IP-adres van de status test en kunnen ertoe leiden dat uw scenario mislukt.

Als u meerdere interfaces op de virtuele machine hebt, moet u zorgen dat u de test op de interface die u hebt ontvangen op reageren.  Mogelijk moet u het bron netwerk adres dit adres in de virtuele machine vertalen per interface.

Schakel TCP- [tijds tempels](https://tools.ietf.org/html/rfc1323)niet in.  Het inschakelen van TCP-tijds tempels kan ertoe leiden dat status controles mislukken omdat TCP-pakketten worden verwijderd door de TCP-stack van het gast besturingssysteem van de virtuele machine, wat leidt tot Load Balancer het respectievelijke eind punt wordt gemarkeerd.  TCP-tijds tempels worden standaard ingeschakeld voor beveiligde VM-installatie kopieën en moeten worden uitgeschakeld.

## <a name="monitoring"></a>Bewaking

Zowel open bare als interne [Standard Load Balancer](load-balancer-standard-overview.md) geven per eind punt en back-end-eindpunt status de waarde met meerdere dimensies via Azure monitor. Deze metrische gegevens kunnen worden gebruikt door andere Azure-Services of partner toepassingen. 

Met Basic Public Load Balancer wordt de status van de status test per back-end-groep weer gegeven via Azure Monitor-Logboeken.  Azure Monitor-logboeken zijn niet beschikbaar voor interne load balancers van de Basic.  U kunt [Azure monitor logboeken](load-balancer-monitor-log.md) gebruiken om de status van de open bare Load Balancer test te controleren en het aantal tests te testen. Logboekregistratie kan worden gebruikt met Power BI of Azure Operational Insights voor statistische gegevens over de integriteitsstatus van de load balancer.

## <a name="limitations"></a>Beperkingen

- HTTPS-tests bieden geen ondersteuning voor wederzijdse verificatie met een clientcertificaat.
- U kunt assumehHealth-tests niet uitvoeren wanneer TCP-tijds tempels zijn ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Standard Load Balancer](load-balancer-standard-overview.md)
- [Maken van een openbare load balancer in Resource Manager met behulp van PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [REST-API voor statuscontroles](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Nieuwe mogelijkheden voor health test met aanvragen [van de Load Balancer Uservoice](https://aka.ms/lbuservoice)
