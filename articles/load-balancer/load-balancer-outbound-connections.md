---
title: Uitgaande verbindingen in Azure
titleSuffix: Azure Load Balancer
description: In dit artikel wordt uitgelegd hoe Azure VM's in staat stelt om te communiceren met openbare internetservices.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 411c06e19b932b441f27a3c7578d847c6dfc1f7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336995"
---
# <a name="outbound-connections-in-azure"></a>Uitgaande verbindingen in Azure

Azure biedt uitgaande connectiviteit voor klantimplementaties via verschillende mechanismen. In dit artikel wordt beschreven wat de scenario's zijn, wanneer ze van toepassing zijn, hoe ze werken en hoe ze moeten worden beheerd.

>[!NOTE] 
>Dit artikel heeft alleen betrekking op implementaties van Resource Manager. Controleer [uitgaande verbindingen (Klassiek)](load-balancer-outbound-connections-classic.md) voor alle klassieke implementatiescenario's in Azure.

Een implementatie in Azure kan communiceren met eindpunten buiten Azure in de openbare IP-adresruimte. Wanneer een instantie een uitgaande stroom naar een bestemming in de openbare IP-adresruimte initieert, brengt Azure het privé-IP-adres dynamisch in kaart aan een openbaar IP-adres. Nadat deze toewijzing is gemaakt, kan retourverkeer voor deze uitgaande stroom ook het privé-IP-adres bereiken waar de stroom is ontstaan.

Azure gebruikt de vertaling van het bronnetwerkadres (SNAT) om deze functie uit te voeren. Wanneer meerdere privé-IP-adressen zich voordoen achter één openbaar IP-adres, gebruikt Azure [poortadresvertaling (PAT)](#pat) om privé-IP-adressen te maskeren. Kortstondige poorten worden gebruikt voor PAT en zijn [vooraf toegewezen](#preallocatedports) op basis van de grootte van het zwembad.

Er zijn meerdere [uitgaande scenario's.](#scenarios) U deze scenario's naar behoefte combineren. Bekijk ze zorgvuldig om de mogelijkheden, beperkingen en patronen te begrijpen die van toepassing zijn op uw implementatiemodel en toepassingsscenario. Bekijk de richtlijnen voor [het beheer van deze scenario's](#snatexhaust).

>[!IMPORTANT] 
>Standard Load Balancer en Standard Public IP introduceren nieuwe vaardigheden en verschillende gedragingen voor uitgaande connectiviteit.  Ze zijn niet hetzelfde als Basic SKU's.  Als u uitgaande connectiviteit wilt wanneer u met standaard SKU's werkt, moet u deze expliciet definiëren met standaard IP-adressen voor openbare apparaten of met standaardopenbare load balancer.  Dit omvat het maken van uitgaande connectiviteit bij het gebruik van een interne Standard Load Balancer.  We raden u aan altijd uitgaande regels te gebruiken voor een standaard openbare load balancer.  [Scenario 3](#defaultsnat) is niet beschikbaar met Standaard SKU.  Dat betekent dat wanneer een interne Standaard Load Balancer wordt gebruikt, u stappen moet ondernemen om uitgaande connectiviteit te maken voor de VM's in de backendpool als uitgaande connectiviteit gewenst is.  In de context van uitgaande connectiviteit gedragen één zelfstandige VM, alle VM's in een beschikbaarheidsset, alle exemplaren in een VMSS zich als een groep. Dit betekent dat als één vm in een beschikbaarheidsset is gekoppeld aan een standaard SKU, alle VM-exemplaren binnen deze beschikbaarheidsset zich nu volgens dezelfde regels gedragen als of ze zijn gekoppeld aan standaard SKU, zelfs als een afzonderlijke instantie er niet rechtstreeks aan is gekoppeld. Dit gedrag wordt ook waargenomen in het geval van een standalone VM met meerdere netwerkinterfacekaarten die aan een load balancer zijn gekoppeld. Als een NIC wordt toegevoegd als een standalone, zal het hetzelfde gedrag hebben. Controleer dit hele document zorgvuldig om de algemene concepten te begrijpen, [standaardloadbalancer](load-balancer-standard-overview.md) te bekijken op verschillen tussen SKU's en [uitgaande regels te](load-balancer-outbound-rules-overview.md)herzien.  Met behulp van uitgaande regels u fijne korrelige controle over alle aspecten van uitgaande connectiviteit.

## <a name="scenario-overview"></a><a name="scenarios"></a>Overzicht van scenario's

Azure Load Balancer en gerelateerde resources worden expliciet gedefinieerd wanneer u [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)gebruikt.  Azure biedt momenteel drie verschillende methoden om uitgaande connectiviteit voor Azure Resource Manager-resources te bereiken. 

| SKU's | Scenario | Methode | IP-protocollen | Beschrijving |
| --- | --- | --- | --- | --- |
| Standaard, Basic | [1. VM met een openbaar IP-adres op instantieniveau (met of zonder load balancer)](#ilpip) | SNAT, havenmaskerading niet gebruikt | TCP, UDP, ICMP, ESP | Azure maakt gebruik van het openbare IP-adres dat is toegewezen aan de IP-configuratie van de NIC van de instantie. De instantie heeft alle tijdelijke poorten beschikbaar. Bij het gebruik van Standard Load Balancer worden [uitgaande regels](load-balancer-outbound-rules-overview.md) niet ondersteund als een openbaar IP-adres is toegewezen aan de virtuele machine. |
| Standaard, Basic | [2. Public Load Balancer gekoppeld aan een VM (geen openbaar IP-adres in de instantie)](#lb) | SNAT met port masquerading (PAT) met behulp van de Load Balancer frontends | TCP, UDP |Azure deelt het openbare IP-adres van de openbare Load Balancer frontends met meerdere privé IP-adressen. Azure maakt gebruik van kortstondige poorten van de frontends naar PAT. U moet [uitgaande regels](load-balancer-outbound-rules-overview.md) gebruiken om uitgaande connectiviteit expliciet te definiëren. |
| none | [3. Zelfstandige VM (geen load balancer, geen openbaar IP-adres)](#defaultsnat) | SNAT met port maskerading (PAT) | TCP, UDP | Azure wijst automatisch een openbaar IP-adres aan voor SNAT, deelt dit openbare IP-adres met meerdere privé-IP-adressen van de beschikbaarheidsset en maakt gebruik van tijdelijke poorten van dit openbare IP-adres. Dit scenario is een terugval voor de voorgaande scenario's. We raden het niet aan als je zichtbaarheid en controle nodig hebt. |

Als u niet wilt dat een VM communiceert met eindpunten buiten Azure in openbare IP-adresruimte, u netwerkbeveiligingsgroepen (NSG's) gebruiken om de toegang te blokkeren als dat nodig is. In [de](#preventoutbound) sectie Voorkomen van uitgaande connectiviteit worden NSG's nader besproken. Richtlijnen voor het ontwerpen, implementeren en beheren van een virtueel netwerk zonder uitgaande toegang vallen buiten het bereik van dit artikel.

### <a name="scenario-1-vm-with-public-ip-address"></a><a name="ilpip"></a>Scenario 1: VM met openbaar IP-adres

In dit scenario heeft de VM een openbaar IP-adres toegewezen. Wat uitgaande verbindingen betreft, maakt het niet uit of de VM in balans is met de belasting of niet. Dit scenario heeft voorrang op de anderen. Wanneer een openbaar IP-adres wordt gebruikt, gebruikt de VM het openbare IP-adres voor alle uitgaande stromen.  

Een openbaar IP dat aan een VM is toegewezen, is een 1:1-relatie (in plaats van 1: veel) en geïmplementeerd als een staatloze 1:1 NAT.  Port masquerading (PAT) wordt niet gebruikt en de VM heeft alle tijdelijke poorten beschikbaar voor gebruik.

Als uw toepassing veel uitgaande stromen initieert en u snat-poortuitputting ondervindt, u overwegen een [openbaar IP-adres toe te wijzen om snat-beperkingen te beperken.](#assignilpip) Review [Het beheer van SNAT-uitputting](#snatexhaust) in zijn geheel.

### <a name="scenario-2-load-balanced-vm-without-a-public-ip-address"></a><a name="lb"></a>Scenario 2: VM met taak in balans brengen zonder openbaar IP-adres

In dit scenario maakt de VM deel uit van een openbare backendpool loadbalancer. De VM heeft geen openbaar IP-adres toegewezen. De load balancer-bron moet zijn geconfigureerd met een regel voor load balancer om een koppeling te maken tussen de openbare IP-frontend met de backendpool.

Als u deze regelconfiguratie niet voltooit, is het gedrag zoals beschreven in het scenario voor [zelfstandige VM zonder openbaar IP.](#defaultsnat) Het is niet nodig dat de regel een werkende luisteraar in de backend pool heeft om de statussonde te laten slagen.

Wanneer de vm met belastingsgebalanceerde een uitgaande stroom maakt, vertaalt Azure het IP-adres van de privébron van de uitgaande stroom naar het openbare IP-adres van de frontend van de openbare load balancer. Azure gebruikt SNAT om deze functie uit te voeren. Azure gebruikt [PAT](#pat) ook om meerdere privé-IP-adressen achter een openbaar IP-adres te maskeren. 

Kortstondige poorten van de openbare IP-adresfrontend van de load balancer worden gebruikt om afzonderlijke stromen van de VM te onderscheiden. SNAT maakt dynamisch gebruik [van vooraf toegewezen efemere poorten](#preallocatedports) wanneer uitgaande stromen worden gemaakt. In deze context worden de kortstondige poorten die voor SNAT worden gebruikt SNAT-poorten genoemd.

SNAT-poorten zijn vooraf toegewezen zoals beschreven in de sectie [Understanding SNAT en PAT.](#snat) Ze zijn een eindige bron die kan worden uitgeput. Het is belangrijk om te begrijpen hoe ze [worden geconsumeerd.](#pat) Als u wilt begrijpen hoe u voor dit verbruik ontwerpen en waar nodig beperken, bekijkt u [het beheer van SNAT-uitputting](#snatexhaust).

Wanneer [meerdere openbare IP-adressen zijn gekoppeld aan Load Balancer Basic,](load-balancer-multivip-overview.md)is een van deze openbare IP-adressen een kandidaat voor uitgaande stromen en wordt er een willekeurig geselecteerd.  

Als u de status van uitgaande verbindingen met Load Balancer Basic wilt controleren, u [Azure Monitor-logboeken voor load balancer-](load-balancer-monitor-log.md) en [waarschuwingsgebeurtenislogboeken](load-balancer-monitor-log.md#alert-event-log) gebruiken om te controleren op SNAT-poortuitputtingsberichten.

### <a name="scenario-3-standalone-vm-without-a-public-ip-address"></a><a name="defaultsnat"></a>Scenario 3: Zelfstandige VM zonder openbaar IP-adres

In dit scenario maakt de VM geen deel uit van een openbare groep Load Balancer (en maakt geen deel uit van een interne groep standaardlastbalansen) en heeft deze geen openbaar IP-adres toegewezen. Wanneer de VM een uitgaande stroom maakt, vertaalt Azure het IP-adres van de privébron van de uitgaande stroom naar een IP-adres van een openbare bron. Het openbare IP-adres dat voor deze uitgaande stroom wordt gebruikt, is niet configureerbaar en telt niet mee voor de openbare IP-bronlimiet van het abonnement. Dit openbare IP-adres is niet van u en kan niet worden gereserveerd. Als u de VM- of beschikbaarheidsset of de virtuele machineschaalset opnieuw implementeert, wordt dit openbare IP-adres vrijgegeven en wordt een nieuw openbaar IP-adres aangevraagd. Gebruik dit scenario niet voor het whitelisten van IP-adressen. Gebruik in plaats daarvan een van de andere twee scenario's waarbij u expliciet het uitgaande scenario en het openbare IP-adres declareert dat moet worden gebruikt voor uitgaande connectiviteit.

>[!IMPORTANT] 
>Dit scenario is ook van toepassing wanneer __alleen__ een interne Basislastbalancer is gekoppeld. Scenario 3 is __niet beschikbaar__ wanneer een interne standaardloadbalancer aan een vm is gekoppeld.  U moet expliciet [scenario 1](#ilpip) of scenario [2](#lb) maken naast het gebruik van een interne StandaardLoad Balancer.

Azure gebruikt SNAT met poortmaskering[(PAT)](#pat)om deze functie uit te voeren. Dit scenario is vergelijkbaar met [scenario 2,](#lb)behalve dat er geen controle is over het gebruikte IP-adres. Dit is een terugvalscenario voor wanneer scenario's 1 en 2 niet bestaan. We raden dit scenario niet aan als u controle wilt over het uitgaande adres. Als uitgaande verbindingen een essentieel onderdeel van uw toepassing zijn, moet u een ander scenario kiezen.

SNAT-poorten zijn vooraf toegewezen zoals beschreven in de sectie [Understanding SNAT en PAT.](#snat)  Het aantal VM's dat een beschikbaarheidsset deelt, bepaalt welke prelocatielaag van toepassing is.  Een standalone VM zonder beschikbaarheidsset is in feite een pool van 1 voor het bepalen van preallocatie (1024 SNAT-poorten). SNAT-poorten zijn een eindige bron die kan worden uitgeput. Het is belangrijk om te begrijpen hoe ze [worden geconsumeerd.](#pat) Als u wilt begrijpen hoe u voor dit verbruik ontwerpen en waar nodig beperken, bekijkt u [het beheer van SNAT-uitputting](#snatexhaust).

### <a name="multiple-combined-scenarios"></a><a name="combinations"></a>Meerdere, gecombineerde scenario's

U de in de voorgaande secties beschreven scenario's combineren om een bepaald resultaat te bereiken. Wanneer er meerdere scenario's aanwezig zijn, is een rangorde van toepassing: [scenario 1](#ilpip) heeft voorrang op [scenario 2](#lb) en [3](#defaultsnat). [Scenario 2](#lb) overschrijft [scenario 3](#defaultsnat).

Een voorbeeld is een Azure Resource Manager-implementatie waarbij de toepassing sterk afhankelijk is van uitgaande verbindingen naar een beperkt aantal bestemmingen, maar ook inkomende stromen ontvangt via een frontend van load balancer. In dit geval u scenario's 1 en 2 combineren voor verlichting. Voor aanvullende patronen, bekijk [het beheer van SNAT-uitputting](#snatexhaust).

### <a name="multiple-frontends-for-outbound-flows"></a><a name="multife"></a>Meerdere frontends voor uitgaande stromen

#### <a name="standard-load-balancer"></a>Standaard load balancer

Standard Load Balancer gebruikt alle kandidaten voor uitgaande stromen op hetzelfde moment dat [meerdere (openbare) IP frontends](load-balancer-multivip-overview.md) aanwezig zijn. Elke frontend vermenigvuldigt het aantal beschikbare vooraf toegewezen SNAT-poorten als een regel voor het balanceren van de last is ingeschakeld voor uitgaande verbindingen.

U ervoor kiezen om een IP-adres aan de frontend te onderdrukken voor uitgaande verbindingen met een nieuwe taakverdelingsregel:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Normaal gesproken `disableOutboundSnat` is de optie standaard _false_ en betekent dat deze regel uitgaande SNAT programmeert voor de bijbehorende VM's in de backendpool van de regel voor het balanceren van de last. Het `disableOutboundSnat` kan worden gewijzigd in _true_ om te voorkomen dat Load Balancer het bijbehorende FRONTEnd IP-adres gebruikt voor uitgaande verbindingen voor de VM's in de backendpool van deze regel voor taakverdeling.  En u ook nog steeds een specifiek IP-adres aanwijzen voor uitgaande stromen zoals beschreven in [meerdere, gecombineerde scenario's.](#combinations)

#### <a name="load-balancer-basic"></a>Basisbalanssaldobasis

Load Balancer Basic kiest één frontend voor uitgaande stromen wanneer [meerdere (openbare) IP frontends](load-balancer-multivip-overview.md) kandidaten zijn voor uitgaande stromen. Deze selectie is niet configureerbaar en u moet het selectiealgoritme als willekeurig beschouwen. U een specifiek IP-adres aanwijzen voor uitgaande stromen zoals beschreven in [meerdere, gecombineerde scenario's.](#combinations)

### <a name="availability-zones"></a><a name="az"></a>Beschikbaarheidszones

Bij het gebruik van [Standard Load Balancer met Availability Zones](load-balancer-standard-availability-zones.md)kunnen zoneredundante frontends zoneredundante uitgaande SNAT-verbindingen bieden en overleeft SNAT-programmering zonefouten.  Wanneer zonale frontends worden gebruikt, delen uitgaande SNAT-verbindingen het lot met de zone waartoe ze behoren.

## <a name="understanding-snat-and-pat"></a><a name="snat"></a>Inzicht in SNAT en PAT

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Port maskerading SNAT (PAT)

Wanneer een openbare Load Balancer-bron is gekoppeld aan VM-exemplaren, wordt elke uitgaande verbindingsbron opnieuw geschreven. De bron wordt herschreven van de private IP-adresruimte van het virtuele netwerk naar het frontend Openbaar IP-adres van de load balancer. In de openbare IP-adresruimte moet de 5-tuple van de stroom (bron-IP-adres, bronpoort, IP-transportprotocol, bestemmings-IP-adres, doelpoort) uniek zijn.  Port masquerading SNAT kan worden gebruikt met TCP- of UDP IP-protocollen.

Efemere ports (SNAT-poorten) worden gebruikt om dit te bereiken na het herschrijven van het IP-adres van de privébron, omdat meerdere stromen afkomstig zijn van één openbaar IP-adres. Het SNAT-algoritme voor de poort maskeren wijst SNAT-poorten anders toe voor UDP versus TCP.

#### <a name="tcp-snat-ports"></a><a name="tcp"></a>TCP SNAT-poorten

Eén SNAT-poort wordt per stroom verbruikt naar één IP-adres voor één bestemming, poort. Voor meerdere TCP-stromen naar hetzelfde doel-IP-adres, -poort en -protocol verbruikt elke TCP-stroom één SNAT-poort. Dit zorgt ervoor dat de stromen uniek zijn wanneer ze afkomstig zijn van hetzelfde openbare IP-adres en naar hetzelfde doel IP-adres, poort en protocol gaan. 

Meerdere stromen, elk naar een ander doel-IP-adres, poort en protocol, delen één SNAT-poort. Het doel-IP-adres, de poort en het protocol maken stromen uniek zonder dat extra bronpoorten nodig zijn om stromen in de openbare IP-adresruimte te onderscheiden.

#### <a name="udp-snat-ports"></a><a name="udp"></a>UDP SNAT-poorten

UDP SNAT-poorten worden beheerd door een ander algoritme dan TCP SNAT-poorten.  Load Balancer maakt gebruik van een algoritme dat bekend staat als "poortbeperkte kegel NAT" voor UDP.  Voor elke stroom wordt één SNAT-poort verbruikt, ongeacht het ip-adres van de bestemming, de poort.

#### <a name="snat-port-reuse"></a>Hergebruik van SNAT-poort

Zodra een poort is vrijgegeven, is de poort beschikbaar voor hergebruik als dat nodig is.  U SNAT-poorten zien als een reeks van laag naar hoogste beschikbaar voor een bepaald scenario en de eerste beschikbare SNAT-poort wordt gebruikt voor nieuwe verbindingen. 
 
#### <a name="exhaustion"></a>Uitputting

Wanneer SNAT-poortbronnen zijn uitgeput, mislukken uitgaande stromen totdat bestaande stromen SNAT-poorten vrijgeven. Load Balancer claimt SNAT-poorten wanneer de stroom wordt gesloten en gebruikt een [idle time-out van 4 minuten](#idletimeout) voor het terugwinnen van SNAT-poorten uit niet-actieve stromen.

UDP SNAT-poorten geven over het algemeen veel sneller uit dan TCP SNAT-poorten vanwege het verschil in gebruikte algoritme. U moet ontwerpen en schalen test met dit verschil in het achterhoofd.

Bekijk de sectie [SNAT beheren](#snatexhaust) voor patronen om omstandigheden te beperken die gewoonlijk leiden tot snat-poortuitputting.

### <a name="ephemeral-port-preallocation-for-port-masquerading-snat-pat"></a><a name="preallocatedports"></a>Kortstondige havenprealalalalvoortoewijzing voor havenmaskerading SNAT (PAT)

Azure gebruikt een algoritme om het aantal vooraf toegewezen SNAT-poorten te bepalen dat beschikbaar is op basis van de grootte van de backendpool bij het gebruik van poortmaskerende SNAT[(PAT).](#pat) SNAT-poorten zijn tijdelijke poorten die beschikbaar zijn voor een bepaald openbaar IP-bronadres.

Hetzelfde aantal SNAT-poorten wordt vooraf toegewezen voor respectievelijk UDP en TCP en wordt onafhankelijk verbruikt per IP-transportprotocol.  Het SNAT-poortgebruik is echter anders, afhankelijk van of de stroom UDP of TCP is.

>[!IMPORTANT]
>Standaard SKU SNAT-programmering is per IP-transportprotocol en afgeleid van de regel voor het balanceren van de last.  Als er alleen een TCP-regel voor taakverdeling bestaat, is SNAT alleen beschikbaar voor TCP. Als u alleen een TCP-regel voor taakverdeling hebt en uitgaande SNAT voor UDP nodig hebt, maakt u een UDP-regel voor taakverdeling van dezelfde frontend naar dezelfde backendpool.  Dit zal leiden tot SNAT programmering voor UDP.  Een werkregel of gezondheidssonde is niet vereist.  Basic SKU SNAT programmeert altijd SNAT voor beide IP-transportprotocol's, ongeacht het transportprotocol dat is opgegeven in de regel voor het balanceren van de lading.

Azure wijst SNAT-poorten vooraf toe aan de IP-configuratie van de NIC van elke VM. Wanneer een IP-configuratie aan de groep wordt toegevoegd, worden de SNAT-poorten vooraf toegewezen voor deze IP-configuratie op basis van de grootte van de backendpool. Wanneer uitgaande stromen worden gemaakt, verbruikt [PAT](#pat) dynamisch (tot de vooraf toegewezen limiet) en geeft deze poorten vrij wanneer de stroom wordt gesloten of [idle time-outs](#idletimeout) plaatsvinden.

In de volgende tabel worden de voortoewijzingen van de SNAT-poort voor lagen van backendpoolgroottes weergegeven:

| Poolgrootte (VM-exemplaren) | Vooraf toegewezen SNAT-poorten per IP-configuratie|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

>[!NOTE]
> Bij het gebruik van Standard Load Balancer met [meerdere frontends](load-balancer-multivip-overview.md)vermenigvuldigt elk IP-adres aan de frontend het aantal beschikbare SNAT-poorten in de vorige tabel. Een backendpool van 50 VM's met 2 load balancing rules, elk met een apart frontend IP-adres, gebruikt bijvoorbeeld 2048 (2x 1024) SNAT-poorten per IP-configuratie. Zie details voor [meerdere frontends](#multife).

Houd er rekening mee dat het aantal beschikbare SNAT-poorten niet direct wordt vertaald naar het aantal stromen. Eén SNAT-poort kan worden hergebruikt voor meerdere unieke bestemmingen. Poorten worden alleen verbruikt als het nodig is om stromen uniek te maken. Raadpleeg voor ontwerp- en mitigatierichtlijnen het gedeelte over [het beheren van deze uitputtende bron](#snatexhaust) en de sectie waarin [PAT](#pat)wordt beschreven.

Als u de grootte van uw backendpool wijzigt, kan dit gevolgen hebben voor sommige van uw gevestigde stromen. Als de grootte van de backendpool toeneemt en overgaat naar de volgende laag, wordt de helft van de vooraf toegewezen SNAT-poorten teruggewonnen tijdens de overgang naar de volgende grotere backendpoollaag. Stromen die zijn gekoppeld aan een teruggewonnen SNAT-poort, krijgen een time-out en moeten opnieuw worden ingesteld. Als een nieuwe stroom wordt geprobeerd, zal de stroom onmiddellijk slagen zolang vooraf toegewezen poorten beschikbaar zijn.

Als de grootte van de backendpool afneemt en overgaat naar een lagere laag, neemt het aantal beschikbare SNAT-poorten toe. In dit geval worden bestaande toegewezen SNAT-poorten en hun respectieve stromen niet beïnvloed.

SNAT-poorten toewijzingen zijn IP-transportprotocol specifiek (TCP en UDP worden afzonderlijk onderhouden) en worden vrijgegeven onder de volgende voorwaarden:

### <a name="tcp-snat-port-release"></a>TCP SNAT-poortrelease

- Als een van beide server/client FINACK verzendt, wordt de SNAT-poort na 240 seconden vrijgegeven.
- Als een RST wordt gezien, wordt de SNAT-poort na 15 seconden vrijgegeven.
- Als de niet-actieve time-out is bereikt, wordt de poort vrijgegeven.

### <a name="udp-snat-port-release"></a>UDP SNAT-poortrelease

- Als de niet-actieve time-out is bereikt, wordt de poort vrijgegeven.

## <a name="problem-solving"></a><a name="problemsolving"></a>Probleemoplossing 

Deze sectie is bedoeld om snat-uitputting te beperken en dat kan optreden met uitgaande verbindingen in Azure.

### <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a>Beheer van SNAT (PAT) poortuitputting
[Kortstondige poorten](#preallocatedports) die worden gebruikt voor [PAT](#pat) zijn een uitputtende bron, zoals beschreven in Standalone VM zonder een [openbaar IP-adres](#defaultsnat) en [load-balanced VM zonder een openbaar IP-adres](#lb).

Als u weet dat u veel uitgaande TCP- of UDP-verbindingen initieert naar hetzelfde doel-IP-adres en dezelfde poort, en u geen uitgaande verbindingen observeert of wordt geadviseerd door ondersteuning dat u SNAT-poorten uitvermoeiendt (vooraf toegewezen [efemere poorten](#preallocatedports) die door [PAT](#pat)worden gebruikt), hebt u verschillende algemene mitigatieopties. Bekijk deze opties en bepaal wat er beschikbaar en het beste is voor uw scenario. Het is mogelijk dat een of meer kan helpen bij het beheren van dit scenario.

Als u problemen ondervindt bij het begrijpen van het uitgaande verbindingsgedrag, u IP-stackstatistieken (netstat) gebruiken. Of het kan handig zijn om verbindingsgedragingen te observeren met behulp van pakketopnames. U deze pakketopnames uitvoeren in het gastbesturingssysteem van uw instantie of Network Watcher gebruiken [voor het vastleggen van pakketten.](../network-watcher/network-watcher-packet-capture-manage-portal.md)

#### <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>De toepassing wijzigen om verbindingen opnieuw te gebruiken 
U de vraag naar tijdelijke poorten die voor SNAT worden gebruikt, verminderen door verbindingen in uw toepassing opnieuw te gebruiken. Dit geldt met name voor protocollen zoals HTTP/1.1, waarbij hergebruik van verbindingen de standaardis. En andere protocollen die HTTP gebruiken als hun transport (bijvoorbeeld REST) kunnen op hun beurt profiteren. 

Hergebruik is altijd beter dan individuele, atomaire TCP-verbindingen voor elke aanvraag. Hergebruik resulteert in meer performante, zeer efficiënte TCP-transacties.

#### <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>De toepassing wijzigen om verbindingspooling te gebruiken
U een verbindingspoolingschema gebruiken in uw toepassing, waarbij aanvragen intern worden verdeeld over een vaste set verbindingen (waar mogelijk opnieuw worden gebruikt). Deze regeling beperkt het aantal tijdelijke poorten in gebruik en creëert een meer voorspelbare omgeving. Deze regeling kan ook de doorvoer van aanvragen verhogen door meerdere gelijktijdige bewerkingen toe te staan wanneer één verbinding het antwoord van een bewerking blokkeert.  

Er bestaat mogelijk al verbindingspooling binnen het framework dat u gebruikt om uw toepassing of de configuratie-instellingen voor uw toepassing te ontwikkelen. U het bundelen van verbindingen combineren met het hergebruik van verbindingen. Uw meerdere aanvragen verbruiken vervolgens een vast, voorspelbaar aantal poorten naar hetzelfde doel-IP-adres en dezelfde poort. De aanvragen profiteren ook van een efficiënt gebruik van TCP-transacties, waardoor latentie en resourcegebruik worden verminderd. UDP-transacties kunnen ook hiervan profiteren, omdat het beheren van het aantal UDP-stromen op zijn beurt uitlaatomstandigheden kan vermijden en het gebruik van de SNAT-poort kan beheren.

#### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>De toepassing wijzigen om minder agressieve logica voor nieuwe try's te gebruiken
Wanneer [vooraf toegewezen efemere poorten](#preallocatedports) die worden gebruikt voor [PAT](#pat) zijn uitgeput of toepassing fouten optreden, agressieve of brute kracht pogingen zonder verval en backoff logica veroorzaken uitputting optreden of aanhouden. U de vraag naar tijdelijke poorten verminderen door een minder agressieve logica voor nieuwe try te gebruiken. 

Kortstondige poorten hebben een idle time-out van 4 minuten (niet instelbaar). Als de pogingen te agressief zijn, heeft de uitputting geen kans om op te helderen op zijn eigen. Daarom is het een essentieel onderdeel van het ontwerp, gezien hoe - en hoe vaak - uw toepassing transacties opnieuw indient.

#### <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Een openbaar IP toewijzen aan elke virtuele machine
Als u een openbaar IP-adres toewijs, wordt uw scenario gewijzigd in [Openbaar IP in een vm.](#ilpip) Alle tijdelijke poorten van het openbare IP-adres die voor elke VM worden gebruikt, zijn beschikbaar voor de VM. (In tegenstelling tot scenario's waarin kortstondige poorten van een openbaar IP worden gedeeld met alle VM's die zijn gekoppeld aan de desbetreffende backendpool.) Er zijn afwegingen te overwegen, zoals de extra kosten van openbare IP-adressen en de mogelijke impact van het whitelisten van een groot aantal individuele IP-adressen.

>[!NOTE] 
>Deze optie is niet beschikbaar voor webworkerrollen.

#### <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Meerdere frontends gebruiken

Wanneer u openbare StandaardloadBalancer gebruikt, wijst u [meerdere frontend IP-adressen](#multife) toe voor uitgaande verbindingen en [vermenigvuldigt u het aantal beschikbare SNAT-poorten.](#preallocatedports)  Maak een frontend IP-configuratie, regel en backend pool om de programmering van SNAT te activeren naar het openbare IP-adres van de frontend.  De regel hoeft niet te functioneren en een gezondheidssonde hoeft niet te slagen.  Als u meerdere frontends ook voor binnenkomende (in plaats van alleen voor uitgaande) gebruikt, moet u aangepaste statussondes goed gebruiken om betrouwbaarheid te garanderen.

>[!NOTE]
>In de meeste gevallen is uitputting van SNAT-poorten een teken van slecht ontwerp.  Zorg ervoor dat u begrijpt waarom u poorten uitvermoeiend voordat u meer frontends gebruikt om SNAT-poorten toe te voegen.  U een probleem maskeren dat kan leiden tot mislukking later.

#### <a name="scale-out"></a><a name="scaleout"></a>Uitschalen

[Vooraf toegewezen poorten](#preallocatedports) worden toegewezen op basis van de grootte van de backendpool en gegroepeerd in lagen om verstoringte minimaliseren wanneer sommige poorten moeten worden herverdeeld om de volgende grotere backendpoolgroottelaag te kunnen huisvesten.  U hebt mogelijk een optie om de intensiteit van het SNAT-poortgebruik voor een bepaalde frontend te verhogen door uw backendpool te schalen naar de maximale grootte voor een bepaalde laag.  Dit vereist dat de toepassing efficiënt wordt opgeschaald.

Twee virtuele machines in de backendpool zouden bijvoorbeeld 1024 SNAT-poorten beschikbaar hebben per IP-configuratie, waardoor in totaal 2048 SNAT-poorten voor de implementatie kunnen worden uitgevoerd.  Als de implementatie zou worden verhoogd tot 50 virtuele machines, hoewel het aantal vooraf toegewezen poorten constant blijft per virtuele machine, kunnen in totaal 51.200 (50 x 1024) SNAT-poorten door de implementatie worden gebruikt.  Als u uw implementatie wilt uitschalen, controleert u het aantal [vooraf toegewezen poorten](#preallocatedports) per laag om ervoor te zorgen dat u uw schaal vormgeeft tot het maximum voor de desbetreffende laag.  In het voorgaande voorbeeld, als u ervoor had gekozen om uit te schalen naar 51 in plaats van 50 exemplaren, zou u doorstromen naar de volgende laag en eindigen met minder SNAT-poorten per VM en in totaal.

Als u uitschaalt naar de volgende grotere backendpoolgroottelaag, is er potentieel voor sommige van uw uitgaande verbindingen om een time-out te maken als toegewezen poorten moeten worden toegewezen.  Als u slechts een deel van uw SNAT-poorten gebruikt, is het inconsequent om uit te schalen naar de volgende grotere backendpoolgrootte.  De helft van de bestaande poorten wordt bij elke backendpoollaag toegewezen.  Als u niet wilt dat dit gebeurt, moet u uw implementatie vormgeven aan de laaggrootte.  Of zorg ervoor dat uw toepassing kan detecteren en opnieuw proberen als dat nodig is.  TCP keepalives kan helpen bij het detecteren wanneer SNAT-poorten niet meer functioneren als gevolg van herverdeling.

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Keepalives gebruiken om de uitgaande idle time-out opnieuw in te stellen

Uitgaande verbindingen hebben een idle time-out van 4 minuten. Deze time-out is instelbaar via [uitgaande regels.](../load-balancer/load-balancer-outbound-rules-overview.md#idletimeout) U ook transport (bijvoorbeeld TCP keepalives) of keepalives voor toepassingslagen gebruiken om een niet-actieve stroom te vernieuwen en indien nodig deze niet-actieve time-out opnieuw in te stellen.  

Bij het gebruik van TCP keepalives is het voldoende om ze aan één kant van de verbinding in te schakelen. Het is bijvoorbeeld voldoende om ze aan de serverkant alleen te laten resetten om de niet-actieve timer van de stroom te resetten en het is niet nodig dat beide partijen TCP keepalives starten.  Vergelijkbare concepten bestaan voor toepassingslaag, waaronder databaseclientserverconfiguraties.  Controleer de serverkant op welke opties er bestaan voor toepassingsspecifieke keepalives.

## <a name="discovering-the-public-ip-that-a-vm-uses"></a><a name="discoveroutbound"></a>Het openbare IP ontdekken dat een VM gebruikt
Er zijn veel manieren om het IP-adres van een uitgaande verbinding te bepalen. OpenDNS biedt een service die u het openbare IP-adres van uw vm kan laten zien. 

Met de opdracht nslookup u een DNS-query verzenden voor de naam myip.opendns.com naar de OpenDNS-resolver. De service retourneert het bron-IP-adres dat is gebruikt om de query te verzenden. Wanneer u de volgende query uitvoert vanaf uw vm, is het antwoord het openbare IP-adres dat voor die vm wordt gebruikt:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Uitgaande connectiviteit voorkomen
Soms is het ongewenst dat een VM een uitgaande stroom mag maken. Of er kan een vereiste zijn om te beheren welke bestemmingen kunnen worden bereikt met uitgaande stromen, of welke bestemmingen kunnen beginnen inkomende stromen. In dit geval u [netwerkbeveiligingsgroepen](../virtual-network/security-overview.md) gebruiken om de bestemmingen te beheren die de VM kan bereiken. U ook NSG's gebruiken om te beheren welke openbare bestemming binnenkomende stromen kan initiëren.

Wanneer u een NSG toepast op een vm die in balans is met de belasting, moet u letten op de [servicetags](../virtual-network/security-overview.md#service-tags) en [de standaardbeveiligingsregels.](../virtual-network/security-overview.md#default-security-rules) U moet ervoor zorgen dat de VM aanvragen voor statussondes van Azure Load Balancer kan ontvangen. 

Als een NSG aanvragen voor statussondes blokkeert van de AZURE_LOADBALANCER standaardtag, mislukt de VM-statussonde en wordt de VM gemarkeerd. Load Balancer stopt met het verzenden van nieuwe stromen naar die VM.

## <a name="limitations"></a>Beperkingen
- Webworker-rollen zonder VNet en andere Microsoft-platformservices kunnen toegankelijk zijn wanneer alleen een interne StandaardloadBalancer wordt gebruikt vanwege een bijwerking van de werking van pre-VNet-services en andere platformservices. Vertrouw niet op deze bijwerking, omdat de betreffende service zelf of het onderliggende platform zonder kennisgeving kan veranderen. U moet er altijd van uitgaan dáár desgewenst uitgaande connectiviteit moeten maken wanneer u alleen een interne standaardloadbalancer gebruikt. Het [standaard SNAT-scenario](#defaultsnat) 3 dat in dit artikel wordt beschreven, is niet beschikbaar.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Standard Load Balancer](load-balancer-standard-overview.md).
- Meer informatie over [uitgaande regels](load-balancer-outbound-rules-overview.md) voor standaard openbare load balancer.
- Meer informatie over [Load Balancer](load-balancer-overview.md).
- Meer informatie over [netwerkbeveiligingsgroepen](../virtual-network/security-overview.md).
- Meer informatie over enkele andere belangrijke [netwerkmogelijkheden](../networking/networking-overview.md) in Azure.
