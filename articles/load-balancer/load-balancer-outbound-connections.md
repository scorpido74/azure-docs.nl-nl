---
title: Uitgaande verbindingen in Azure
titleSuffix: Azure Load Balancer
description: In dit artikel wordt uitgelegd hoe Azure virtuele machines kan communiceren met open bare Internet Services.
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
ms.openlocfilehash: 5bdcd955919a91760f16287a62956542cfaa47c5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225286"
---
# <a name="outbound-connections-in-azure"></a>Uitgaande verbindingen in Azure

Azure biedt uitgaande connectiviteit voor klant implementaties via verschillende mechanismen. In dit artikel wordt beschreven wat de scenario's zijn, wanneer deze van toepassing zijn, hoe ze werken en hoe u ze beheert.

>[!NOTE] 
>In dit artikel worden alleen de implementaties van Resource Manager besproken. Controleer de [uitgaande verbindingen (klassiek)](load-balancer-outbound-connections-classic.md) voor alle klassieke implementatie Scenario's in Azure.

Een implementatie in azure kan communiceren met eind punten buiten Azure in de open bare IP-adres ruimte. Wanneer een exemplaar een uitgaande stroom initieert naar een bestemming in de open bare IP-adres ruimte, wijst Azure het privé-IP-adres dynamisch toe aan een openbaar IP-adres. Nadat deze toewijzing is gemaakt, kan het retour verkeer voor deze uitgaande stroom van het doel ook het privé-IP-adres bereiken waarvan de stroom afkomstig is.

Azure gebruikt bron Network Address Translation (SNAT) om deze functie uit te voeren. Wanneer meerdere privé-IP-adressen worden gemaskeerd achter één openbaar IP-adres, gebruikt Azure [poort adres omzetting (Pat)](#pat) voor het maskeren van privé-IP-adressen. Tijdelijke poorten worden gebruikt voor PAT en zijn [vooraf toegewezen](#preallocatedports) op basis van pool grootte.

Er zijn meerdere [uitgaande scenario's](#scenarios). U kunt deze scenario's zo nodig combi neren. Lees deze zorgvuldig door om inzicht te krijgen in de mogelijkheden, beperkingen en patronen zoals die van toepassing zijn op uw implementatie model en toepassings scenario. Raadpleeg de richt lijnen voor het [beheren van deze scenario's](#snatexhaust).

>[!IMPORTANT] 
>Standard Load Balancer en standaard open bare IP introduceren nieuwe mogelijkheden en verschillende gedragingen voor uitgaande connectiviteit.  Ze zijn niet hetzelfde als de basis-Sku's.  Als u een uitgaande verbinding wilt gebruiken bij het werken met standaard-Sku's, moet u deze expliciet definiëren met een openbaar IP-adres of standaard open bare Load Balancer.  Dit omvat het maken van uitgaande connectiviteit bij het gebruik van een interne Standard Load Balancer.  U wordt aangeraden om altijd uitgaande regels te gebruiken op een standaard open bare Load Balancer.  [Scenario 3](#defaultsnat) is niet beschikbaar voor de standaard-SKU.  Dit betekent dat als er een interne Standard Load Balancer wordt gebruikt, u stappen moet nemen om uitgaande connectiviteit te maken voor de virtuele machines in de back-endadresgroep als uitgaande connectiviteit gewenst is.  In de context van uitgaande connectiviteit, één zelfstandige virtuele machine, alle virtuele machines in een Beschikbaarheidsset, werken alle exemplaren in een VMSS als groep. Dit betekent dat als er één virtuele machine in een Beschikbaarheidsset is gekoppeld aan een standaard-SKU, alle VM-exemplaren binnen deze Beschikbaarheidsset nu dezelfde regels gedragen als voor de standaard-SKU, zelfs als een afzonderlijke instantie niet rechtstreeks is gekoppeld.  Lees dit volledige document aandachtig door om inzicht te krijgen in de algemene concepten, Lees [Standard Load Balancer](load-balancer-standard-overview.md) voor verschillen tussen sku's en controleer de [regels voor uitgaande verbindingen](load-balancer-outbound-rules-overview.md).  Met uitgaande regels kunt u de controle over alle aspecten van de uitgaande connectiviteit nauw keuriger maken.

## <a name="scenarios"></a>Scenario-overzicht

Azure Load Balancer en gerelateerde resources worden expliciet gedefinieerd wanneer u [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)gebruikt.  Azure biedt momenteel drie verschillende methoden voor het bezorgen van uitgaande connectiviteit voor Azure Resource Manager-resources. 

| Voorraad | Scenario | Methode | IP-protocollen | Beschrijving |
| --- | --- | --- | --- | --- |
| Standaard, basis | [1. VM met openbaar IP-adres (met of zonder Load Balancer)](#ilpip) | SNAT, poort masker niet gebruikt | TCP, UDP, ICMP, ESP | Azure gebruikt het open bare IP-adres dat is toegewezen aan de IP-configuratie van de NIC van de instantie. Alle tijdelijke poorten zijn beschikbaar voor het exemplaar. Wanneer u Standard Load Balancer gebruikt, moet u [Uitgaande regels](load-balancer-outbound-rules-overview.md) gebruiken om de uitgaande connectiviteit expliciet te definiëren |
| Standaard, basis | [2. open bare Load Balancer gekoppeld aan een virtuele machine (geen openbaar IP-adres op het exemplaar)](#lb) | SNAT met poort maskering (PAT) met behulp van de Load Balancer-frontends | TCP, UDP |Azure deelt het open bare IP-adres van de open bare front Load Balancer-front-end met meerdere privé IP-adressen. Azure gebruikt tijdelijke poorten van de front-ends tot PAT. |
| geen of basis | [3. zelfstandige VM (niet Load Balancer, geen openbaar IP-adres)](#defaultsnat) | SNAT met poort maskering (PAT) | TCP, UDP | Azure wijst automatisch een openbaar IP-adres toe voor SNAT, deelt dit open bare IP-adres met meerdere privé IP-adressen van de beschikbaarheidsset en maakt gebruik van tijdelijke poorten van dit open bare IP-adres. Dit scenario is een terugval van de voor gaande scenario's. Het is niet raadzaam om het te controleren en te beheren. |

Als u niet wilt dat een virtuele machine communiceert met eind punten buiten Azure in een open bare IP-adres ruimte, kunt u netwerk beveiligings groepen (Nsg's) gebruiken om de toegang naar behoefte te blok keren. In de sectie wordt voor [komen dat uitgaande connectiviteit](#preventoutbound) nsg's meer details bevat. Richt lijnen voor het ontwerpen, implementeren en beheren van een virtueel netwerk zonder uitgaande toegang valt buiten het bereik van dit artikel.

### <a name="ilpip"></a>Scenario 1: virtuele machine met openbaar IP-adres

In dit scenario wordt er een openbaar IP-adres aan de virtuele machine toegewezen. Voor uitgaande verbindingen is het niet van belang of de virtuele machine gelijkmatig is verdeeld of niet. Dit scenario heeft voor rang op de andere. Wanneer een openbaar IP-adres wordt gebruikt, gebruikt de virtuele machine het open bare IP-adres voor alle uitgaande stromen.  

Een openbaar IP-adres dat is toegewezen aan een virtuele machine is een 1:1-relatie (in plaats van 1: veel) en geïmplementeerd als een stateless 1:1 NAT.  Poort maskering (PAT) wordt niet gebruikt en de virtuele machine heeft alle tijdelijke poorten die beschikbaar zijn voor gebruik.

Als uw toepassing veel uitgaande stromen initieert en u de SNAT-poort uitputtt, kunt u overwegen een [openbaar IP-adres toe te wijzen om de SNAT-beperkingen te beperken](#assignilpip). Bekijk het beheer van de [SNAT-uitputting](#snatexhaust) in zijn geheel.

### <a name="lb"></a>Scenario 2: virtuele machine met taak verdeling zonder openbaar IP-adres

In dit scenario maakt de VM deel uit van een open bare Load Balancer back-end-groep. Aan de virtuele machine is geen openbaar IP-adres toegewezen. De Load Balancer resource moet worden geconfigureerd met een load balancer regel om een koppeling te maken tussen de open bare IP-frontend met de back-end-groep.

Als u deze regel configuratie niet voltooit, is het gedrag zoals beschreven in het scenario voor [zelfstandige virtuele machine zonder openbaar IP-adres](#defaultsnat). Het is niet nodig dat de regel een werkende listener in de back-end-pool heeft om de status test te laten slagen.

Wanneer de virtuele machine met taak verdeling een uitgaande stroom maakt, vertaalt Azure het IP-adres van de particuliere bron van de uitgaande stroom naar het open bare IP-adres van de open bare frontend van Load Balancer. Azure gebruikt SNAT om deze functie uit te voeren. Azure gebruikt ook [Pat](#pat) om meerdere privé-IP-adressen achter een openbaar IP-adres te maskeren. 

Tijdelijke poorten van de open bare IP-adres-frontend van de load balancer worden gebruikt voor het onderscheiden van de afzonderlijke stromen die afkomstig zijn van de virtuele machine. SNAT maakt dynamisch gebruik van [vooraf toegewezen tijdelijke poorten](#preallocatedports) wanneer uitgaande stromen worden gemaakt. In deze context worden de tijdelijke poorten die worden gebruikt voor SNAT de SNAT-poorten genoemd.

SNAT-poorten worden vooraf toegewezen, zoals beschreven in de sectie [over SNAT en Pat](#snat) . Ze zijn een eindige resource die kan worden uitgeput. Het is belang rijk om te begrijpen hoe ze worden [verbruikt](#pat). Als u wilt weten hoe u dit verbruik kunt ontwerpen en zo nodig kunt beperken, raadpleegt u de [beheer SNAT-uitputting](#snatexhaust).

Wanneer [meerdere open bare IP-adressen zijn gekoppeld aan Load Balancer Basic](load-balancer-multivip-overview.md), zijn een van deze open bare IP-adressen een kandidaat voor uitgaande stromen en wordt er een wille keurige selectie geselecteerd.  

Als u de status van uitgaande verbindingen met Load Balancer Basic wilt bewaken, kunt u [Azure monitor-Logboeken gebruiken voor Load Balancer](load-balancer-monitor-log.md) en [waarschuwings gebeurtenis logboeken](load-balancer-monitor-log.md#alert-event-log) om te controleren op berichten over SNAT-poort uitputting.

### <a name="defaultsnat"></a>Scenario 3: zelfstandige VM zonder openbaar IP-adres

In dit scenario maakt de virtuele machine geen deel uit van een open bare Load Balancer groep (en maakt geen deel uit van een interne Standard Load Balancer groep) en waaraan geen openbaar IP-adres is toegewezen. Wanneer de virtuele machine een uitgaande stroom maakt, vertaalt Azure het IP-adres van de privé bron van de uitgaande stroom naar een IP-adres van een open bare bron. Het open bare IP-adres dat voor deze uitgaande stroom wordt gebruikt, kan niet worden geconfigureerd en telt niet op basis van de open bare IP-resource limiet van het abonnement. Dit open bare IP-adres behoort niet tot u en kan niet worden gereserveerd. Als u de virtuele machine of Beschikbaarheidsset of de VM-schaalset opnieuw implementeert, wordt dit open bare IP-adres vrijgegeven en wordt er een nieuw openbaar IP-adres aangevraagd. Gebruik dit scenario niet voor White List IP-adressen. Gebruik in plaats daarvan een van de andere twee scenario's waarin u het uitgaande scenario expliciet declareert en het open bare IP-adres dat moet worden gebruikt voor uitgaande verbindingen.

>[!IMPORTANT] 
>Dit scenario is ook van toepassing wanneer __alleen__ een interne basis Load Balancer is gekoppeld. Scenario 3 is __niet beschikbaar__ wanneer een interne Standard Load Balancer is gekoppeld aan een virtuele machine.  U moet een [scenario 1](#ilpip) of [scenario 2](#lb) expliciet maken naast het gebruik van een interne Standard Load Balancer.

Azure gebruikt SNAT met poort maskering ([Pat](#pat)) om deze functie uit te voeren. Dit scenario is vergelijkbaar met [scenario 2](#lb), maar er is geen controle over het gebruikte IP-adres. Dit is een terugval scenario voor situaties waarin de scenario's 1 en 2 niet bestaan. Dit scenario wordt niet aanbevolen als u het uitgaande adres wilt beheren. Als uitgaande verbindingen een belang rijk onderdeel zijn van uw toepassing, moet u een ander scenario kiezen.

SNAT-poorten zijn vooraf toegewezen, zoals beschreven in de sectie [over SNAT en Pat](#snat) .  Het aantal Vm's dat een Beschikbaarheidsset deelt, bepaalt welke voortoewijzings laag van toepassing is.  Een zelfstandige virtuele machine zonder een Beschikbaarheidsset is in feite een pool van 1 voor het bepalen van de voor toewijzing (1024 SNAT-poorten). SNAT-poorten zijn een eindige resource die kan worden uitgeput. Het is belang rijk om te begrijpen hoe ze worden [verbruikt](#pat). Als u wilt weten hoe u dit verbruik kunt ontwerpen en zo nodig kunt beperken, raadpleegt u de [beheer SNAT-uitputting](#snatexhaust).

### <a name="combinations"></a>Meerdere, gecombineerde scenario's

U kunt de scenario's die in de voor gaande secties worden beschreven, combi neren om een bepaald resultaat te krijgen. Wanneer er meerdere scenario's aanwezig zijn, geldt een volg orde van prioriteit: [scenario 1](#ilpip) heeft voor rang op [scenario 2](#lb) en [3](#defaultsnat). [Scenario 2](#lb) overschrijft [scenario 3](#defaultsnat).

Een voor beeld is een Azure Resource Manager-implementatie waarbij de toepassing intensief gebruikmaakt van uitgaande verbindingen met een beperkt aantal doelen, maar ook inkomende stromen ontvangt via een Load Balancer front-end. In dit geval kunt u scenario 1 en 2 combi neren voor vrijs telling. Zie voor aanvullende patronen [beheer van SNAT-uitputting](#snatexhaust).

### <a name="multife"></a>Meerdere frontends voor uitgaande stromen

#### <a name="standard-load-balancer"></a>Standaardversie van Load Balancer

Standard Load Balancer gebruikt alle kandidaten voor uitgaande stromen tegelijk wanneer [meerdere (open bare) IP-frontends](load-balancer-multivip-overview.md) aanwezig zijn. Elke frontend vermenigvuldigt het aantal beschik bare, vooraf toegewezen SNAT-poorten als een taakverdelings regel is ingeschakeld voor uitgaande verbindingen.

U kunt ervoor kiezen om een frontend-IP-adres dat wordt gebruikt voor uitgaande verbindingen, te onderdrukken met een nieuwe regel optie voor taak verdeling:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Normaal gesp roken wordt de optie `disableOutboundSnat` standaard ingesteld op _False_ en geeft dit aan dat deze regel verloopt uitgaande SNAT voor de gekoppelde virtuele machines in de back-endadresgroep van de taakverdelings regel. De `disableOutboundSnat` kan worden gewijzigd in _True_ om te voor komen dat Load Balancer het gekoppelde frontend-IP-adres gebruikt voor uitgaande verbindingen voor de virtuele machines in de back-endserver van deze taakverdelings regel.  Daarnaast kunt u ook nog steeds een specifiek IP-adres voor uitgaande stromen opgeven, zoals wordt beschreven in [meerdere, gecombineerde scenario's](#combinations) .

#### <a name="load-balancer-basic"></a>Load Balancer Basic

Load Balancer Basic kiest voor een eenmalige front-end voor uitgaande stromen wanneer [meerdere (open bare) IP-frontends](load-balancer-multivip-overview.md) kandidaten voor uitgaande stromen zijn. Deze selectie kan niet worden geconfigureerd. u kunt het selectie algoritme het beste wille keurig beschouwen. U kunt een specifiek IP-adres voor uitgaande stromen opgeven, zoals beschreven in [meerdere, gecombineerde scenario's](#combinations).

### <a name="az"></a>Beschikbaarheidszones

Wanneer u [Standard Load Balancer met Beschikbaarheidszones](load-balancer-standard-availability-zones.md)gebruikt, kan de zone-redundante front-ends zone-redundante uitgaande SNAT-verbindingen bieden en kan er met de SNAT-programmering een zone storing optreden.  Wanneer zonegebonden-front-ends worden gebruikt, delen uitgaande SNAT-verbindingen verspreiding met de zone waartoe ze behoren.

## <a name="snat"></a>Meer informatie over SNAT en PAT

### <a name="pat"></a>Poort maskering SNAT (PAT)

Wanneer een open bare Load Balancer resource is gekoppeld aan VM-exemplaren, wordt elke uitgaande verbindings bron herschreven. De bron wordt herschreven van de privé-IP-adres ruimte van het virtuele netwerk naar het open bare IP-adres van de load balancer. In de open bare IP-adres ruimte moet de 5-tuple van de stroom (bron-IP-adres, bron poort, IP-transport protocol, doel-IP-adres, doel poort) uniek zijn.  Het SNAT-poort masker kan worden gebruikt met TCP-of UDP IP-protocollen.

Tijdelijke poorten (SNAT-poorten) worden gebruikt om dit te verhelpen na het herschrijven van het IP-adres van de privé bron, omdat meerdere stromen afkomstig zijn van één openbaar IP-adres. Met het SNAT-algoritme voor poort maskering worden de SNAT-poorten anders toegewezen voor UDP versus TCP.

#### <a name="tcp"></a>TCP-SNAT-poorten

Eén SNAT-poort wordt per stroom verbruikt naar één doel-IP-adres, poort. Voor meerdere TCP-stromen naar hetzelfde doel-IP-adres, dezelfde poort en hetzelfde protocol gebruikt elke TCP-stroom één SNAT-poort. Dit zorgt ervoor dat de stromen uniek zijn wanneer ze afkomstig zijn uit hetzelfde open bare IP-adres en naar hetzelfde doel-IP-adres, dezelfde poort en hetzelfde protocol gaan. 

Meerdere stromen, elk naar een ander doel-IP-adres, poort en protocol, delen één SNAT-poort. Het doel-IP-adres, de poort en het Protocol maken stroom uniek zonder dat er extra bron poorten nodig zijn om stromen te onderscheiden van de open bare IP-adres ruimte.

#### <a name="udp"></a>UDP SNAT-poorten

UDP SNAT-poorten worden beheerd door een ander algoritme dan TCP-SNAT-poorten.  Load Balancer gebruikt een algoritme dat wordt aangeduid als ' Port-restricted kegel NAT ' voor UDP.  Er wordt één SNAT-poort verbruikt voor elke stroom, ongeacht het doel-IP-adres, de poort.

#### <a name="snat-port-reuse"></a>SNAT-poort opnieuw gebruiken

Zodra een poort is vrijgegeven, kan de poort zo nodig opnieuw worden gebruikt.  U kunt SNAT-poorten beschouwen als een reeks van laag naar Maxi maal beschikbaar voor een bepaald scenario en de eerste beschik bare SNAT-poort wordt gebruikt voor nieuwe verbindingen. 
 
#### <a name="exhaustion"></a>Uitputting

Wanneer de SNAT-poort resources zijn uitgeput, mislukken uitgaande stromen totdat bestaande stromen van SNAT-poorten worden vrijgegeven. Load Balancer overschrijden de SNAT-poorten wanneer de stroom wordt gesloten en gebruikt een [time-out van 4 minuten](#idletimeout) voor het vrijmaken van de SNAT-poorten van niet-actieve stromen.

UDP SNAT-poorten lopen doorgaans veel sneller dan TCP SNAT-poorten vanwege het verschil in gebruikte algoritmen. U moet de test met dit verschil ontwerpen en schalen.

Raadpleeg de sectie [SNAT beheren](#snatexhaust) als u patronen wilt beperken die vaak leiden tot een SNAT-poort uitputting.

### <a name="preallocatedports"></a>Tijdelijke poort voortoewijzing voor poort maskering SNAT (PAT)

Azure gebruikt een algoritme om te bepalen hoeveel vooraf toegewezen SNAT-poorten beschikbaar zijn op basis van de grootte van de back-end-pool wanneer poort maskering SNAT ([Pat](#pat)) wordt gebruikt. SNAT-poorten zijn tijdelijke poorten die beschikbaar zijn voor een bepaald openbaar IP-bron adres.

Hetzelfde aantal SNAT-poorten wordt vooraf toegewezen voor UDP en TCP, respectievelijk onafhankelijk per IP-transport protocol.  Het SNAT-poort gebruik is echter anders, afhankelijk van het feit of de stroom UDP of TCP is.

>[!IMPORTANT]
>Standaard-SKU SNAT-programmering is per IP-transport protocol en afgeleid van de taakverdelings regel.  Als er slechts een regel voor TCP-taak verdeling bestaat, is SNAT alleen beschikbaar voor TCP. Als u alleen een TCP-taakverdelings regel hebt en uitgaand SNAT voor UDP nodig hebt, maakt u een UDP-taakverdelings regel van dezelfde frontend naar dezelfde back-end-groep.  Hiermee wordt SNAT-programmering geactiveerd voor UDP.  Een werk regel of status test is niet vereist.  Basic SKU SNAT always Program ma's SNAT voor IP-transport protocol, ongeacht het transport protocol dat is opgegeven in de taakverdelings regel.

Azure wijst SNAT-poorten vooraf toe aan de IP-configuratie van de NIC van elke virtuele machine. Wanneer een IP-configuratie wordt toegevoegd aan de pool, worden de SNAT-poorten vooraf toegewezen voor deze IP-configuratie op basis van de grootte van de back-endserver. Wanneer uitgaande stromen worden gemaakt, gebruikt [Pat](#pat) dynamisch (Maxi maal de vooraf toegewezen limiet) en worden deze poorten vrijgegeven wanneer de stroom wordt gesloten of er [time-outs voor inactiviteit](#idletimeout) optreden.

In de volgende tabel ziet u de SNAT-poort voortoewijzingen voor lagen van back-endadresgroep-Pools:

| Pool grootte (VM-exemplaren) | Vooraf toegewezen SNAT-poorten per IP-configuratie|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

>[!NOTE]
> Wanneer u Standard Load Balancer met [meerdere frontends](load-balancer-multivip-overview.md)gebruikt, vermenigvuldigt elk frontend-IP-adres het aantal beschik bare SNAT-poorten in de vorige tabel. Bijvoorbeeld, een back-end-pool van 50 VM met 2 taakverdelings regels, elk met een afzonderlijk frontend-IP-adres, gebruikt 2048 (2x 1024) SNAT-poorten per IP-configuratie. Bekijk de Details voor [meerdere frontends](#multife).

Houd er rekening mee dat het aantal beschik bare SNAT-poorten niet rechtstreeks naar het aantal stromen kan worden vertaald. Eén SNAT-poort kan opnieuw worden gebruikt voor meerdere unieke bestemmingen. Poorten worden alleen gebruikt als het nodig is om stromen uniek te maken. Raadpleeg de sectie over het [beheren van deze exhaustible-resource](#snatexhaust) en de sectie met [Pat](#pat)voor instructies voor het ontwerpen en beperken van problemen.

Het wijzigen van de grootte van uw back-end-pool kan invloed hebben op sommige van uw vastgelegde stromen. Als de grootte van de back-end-groep toeneemt en overschakelt naar de volgende laag, worden de helft van uw vooraf toegewezen SNAT-poorten vrijgemaakt tijdens de overgang naar de volgende grotere back-endadresgroep. Stromen die zijn gekoppeld aan een vrijgestelde SNAT-poort, worden getimed en moeten opnieuw worden ingesteld. Als er een nieuwe stroom wordt gedaan, wordt de stroom onmiddellijk uitgevoerd zolang er vooraf toegewezen poorten beschikbaar zijn.

Als de back-end-pool grootte vermindert en overgangen naar een lagere laag, neemt het aantal beschik bare SNAT-poorten toe. In dit geval worden bestaande toegewezen SNAT-poorten en de bijbehorende stromen niet beïnvloed.

Toewijzing van SNAT-poorten is een specifiek IP-transport protocol (TCP en UDP worden afzonderlijk onderhouden) en worden vrijgegeven onder de volgende voor waarden:

### <a name="tcp-snat-port-release"></a>TCP SNAT-poort release

- Als de server/client FINACK verzendt, wordt de SNAT-poort na 240 seconden vrijgegeven.
- Als een RST wordt gezien, wordt de SNAT-poort na 15 seconden vrijgegeven.
- Als er een time-out voor inactiviteit is bereikt, wordt de poort vrijgegeven.

### <a name="udp-snat-port-release"></a>UDP SNAT-poort release

- Als er een time-out voor inactiviteit is bereikt, wordt de poort vrijgegeven.

## <a name="problemsolving"></a>Probleem oplossen 

Deze sectie is bedoeld om te helpen bij het verminderen van de SNAT-uitputting en die kan optreden bij uitgaande verbindingen in Azure.

### <a name="snatexhaust"></a>Poort uitputting van SNAT (PAT) beheren
[Tijdelijke poorten](#preallocatedports) die worden gebruikt voor [Pat](#pat) zijn een exhaustible-resource, zoals beschreven in [zelfstandige virtuele machine zonder openbaar IP-adres](#defaultsnat) en [VM met gelijke taak verdeling zonder openbaar IP-adres](#lb).

Als u weet dat u een groot aantal uitgaande TCP-of UDP-verbindingen met hetzelfde doel-IP-adres en dezelfde poort wilt initiëren, kunt u zien dat uitgaande verbindingen mislukken of worden aanbevolen door ondersteuning te bieden voor de SNAT-poorten (vooraf toegewezen [tijdelijke poorten](#preallocatedports) die worden gebruikt door [Pat](#pat)), hebt u verschillende algemene opties voor risico beperking. Bekijk deze opties en beslis wat er beschikbaar en beste is voor uw scenario. Het is mogelijk dat een of meer informatie kan helpen bij het beheren van dit scenario.

Als u problemen ondervindt met het gedrag van uitgaande verbindingen, kunt u IP-stack statistieken (netstat) gebruiken. Het kan ook handig zijn om verbindings gedrag te observeren met behulp van pakket opnamen. U kunt deze pakket opnames uitvoeren in het gast besturingssysteem van uw exemplaar of gebruikmaken van [Network Watcher voor pakket opname](../network-watcher/network-watcher-packet-capture-manage-portal.md).

#### <a name="connectionreuse"></a>Wijzig de toepassing om verbindingen opnieuw te gebruiken 
U kunt de vraag beperken voor tijdelijke poorten die worden gebruikt voor SNAT door verbindingen in uw toepassing opnieuw te gebruiken. Dit geldt met name voor protocollen zoals HTTP/1.1, waarbij hergebruik van verbindingen de standaard instelling is. En andere protocollen die gebruikmaken van HTTP als Trans Port (bijvoorbeeld REST), kunnen op hun beurt profiteren van. 

Hergebruik is altijd beter dan afzonderlijke, atomische TCP-verbindingen voor elke aanvraag. Het opnieuw gebruiken van resultaten in meer uitvoerende, zeer efficiënte TCP-trans acties.

#### <a name="connection pooling"></a>De toepassing wijzigen voor het gebruik van groepsgewijze verbindingen
U kunt een schema voor groepsgewijze verbindingen gebruiken in uw toepassing, waarbij aanvragen intern worden gedistribueerd via een vaste set verbindingen (waarbij elk gebruik waar mogelijk wordt gebruikt). Dit schema beperkt het aantal tijdelijke poorten dat in gebruik is en maakt een meer voorspel bare omgeving. Dit schema kan ook de door Voer van aanvragen verhogen door meerdere gelijktijdige bewerkingen toe te staan wanneer één verbinding wordt geblokkeerd bij het beantwoorden van een bewerking.  

Verbindings groeperingen kunnen al bestaan in het Framework dat u gebruikt voor het ontwikkelen van uw toepassing of de configuratie-instellingen voor uw toepassing. U kunt groepsgewijze verbindingen combi neren met het opnieuw gebruiken van verbindingen. Uw meerdere aanvragen verbruiken vervolgens een vast, voorspelbaar aantal poorten op hetzelfde doel-IP-adres en dezelfde poort. De aanvragen profiteren ook van efficiënt gebruik van TCP-trans acties waarbij de latentie en het resource gebruik worden verminderd. UDP-trans acties kunnen ook profiteren van het voor deel, omdat het beheer van het aantal UDP-stromen mogelijk is om de uitlaat omstandigheden te voor komen en het SNAT-poort gebruik te beheren.

#### <a name="retry logic"></a>De toepassing wijzigen voor het gebruik van minder agressieve nieuwe logica
Wanneer [tijdelijke poorten](#preallocatedports) die worden gebruikt voor [Pat](#pat) , worden uitgeput of er fouten in de toepassing optreden, worden pogingen voor agressieve of brute Forces zonder onverwachting en uitstel Logic uitputting veroorzaakt of blijven ze behouden. U kunt de vraag naar tijdelijke poorten verminderen door een minder agressieve pogings logica te gebruiken. 

Tijdelijke poorten hebben een time-out van 4 minuten (niet aanpasbaar). Als de nieuwe pogingen te agressief zijn, heeft de uitputting geen mogelijkheid om zichzelf zelf te wissen. Daarom is het een belang rijk onderdeel van het ontwerp om te overwegen hoe--en hoe vaak uw toepassingen nieuwe pogingen doen.

#### <a name="assignilpip"></a>Een openbaar IP-adres toewijzen aan elke VM
Als u een openbaar IP-adres toewijst, wordt uw scenario gewijzigd in [een open bare IP voor een VM](#ilpip). Alle tijdelijke poorten van het open bare IP-adres dat voor elke VM worden gebruikt, zijn beschikbaar voor de VM. (In tegens telling tot scenario's waarbij tijdelijke poorten van een openbaar IP-adres worden gedeeld met alle virtuele machines die zijn gekoppeld aan de bijbehorende back-end-groep) Er zijn zaken die u moet overwegen, zoals de extra kosten van open bare IP-adressen en de mogelijke impact van White List op een groot aantal afzonderlijke IP-adressen.

>[!NOTE] 
>Deze optie is niet beschikbaar voor Web Worker-rollen.

#### <a name="multifesnat"></a>Meerdere frontends gebruiken

Wanneer u open bare Standard Load Balancer gebruikt, wijst u [meerdere frontend-IP-adressen toe voor uitgaande verbindingen](#multife) en [vermenigvuldigt u het aantal beschik bare SNAT-poorten](#preallocatedports).  Een front-end-IP-configuratie, regel en back-end-pool maken om de programmering van SNAT aan het open bare IP-adres van de frontend te activeren.  De regel hoeft niet te functioneren en een status test hoeft niet te slagen.  Als u meerdere front-ends gebruikt voor inkomend verkeer (in plaats van alleen voor uitgaand verkeer), moet u aangepaste status tests gebruiken om de betrouw baarheid te garanderen.

>[!NOTE]
>In de meeste gevallen is de uitputting van de SNAT-poorten een teken van een slecht ontwerp.  Zorg ervoor dat u weet waarom u poorten uitgeput voordat u meer frontends gebruikt om SNAT-poorten toe te voegen.  Het kan zijn dat u een probleem maskeert dat later kan optreden.

#### <a name="scaleout"></a>Uitschalen

Vooraf [toegewezen poorten](#preallocatedports) worden toegewezen op basis van de grootte van de back-endserver en gegroepeerd in lagen om onderbrekingen te minimaliseren wanneer een aantal poorten opnieuw moet worden toegewezen om de eerstvolgende grotere laag van de back-endadresgroep te maken.  Mogelijk hebt u de mogelijkheid om de intensiteit van het SNAT-poort gebruik voor een bepaalde frontend te verhogen door de back-endadresgroep te schalen naar de maximale grootte voor een bepaalde laag.  Dit vereist dat de toepassing efficiënt kan worden geschaald.

Twee virtuele machines in de back-end-pool zouden bijvoorbeeld 1024 SNAT-poorten beschikbaar hebben per IP-configuratie, waardoor er een totaal van 2048 SNAT-poorten voor de implementatie mogelijk is.  Als de implementatie moet worden verhoogd tot 50 virtuele machines, hoewel het aantal vooraf toegewezen poorten constant per virtuele machine blijft, kunnen er in totaal 51.200 (50 x 1024) SNAT-poorten worden gebruikt door de implementatie.  Als u uw implementatie wilt uitschalen, controleert u het aantal [vooraf toegewezen poorten](#preallocatedports) per laag om ervoor te zorgen dat u uw uitschalen afshapet naar het maximum voor de betreffende laag.  Als u in het vorige voor beeld hebt gekozen om uit te schalen naar 51 in plaats van met 50-instanties, gaat u naar de volgende laag en eindigt u met minder SNAT-poorten per VM en in totaal.

Als u uitbreidt naar de volgende grotere laag van de back-endadresgroep, is er mogelijk een time-out opgestaan voor sommige van uw uitgaande verbindingen als toegewezen poorten opnieuw moeten worden toegewezen.  Als u slechts een aantal SNAT-poorten gebruikt, kunt u uitschalen over de volgende grotere back-inconsequential.  De helft van de bestaande poorten wordt opnieuw toegewezen telkens wanneer u overstapt naar de volgende back-endadresgroep.  Als u dit niet wilt doen, moet u uw implementatie vorm geven in de laag grootte.  Of zorg ervoor dat uw toepassing zo nodig kan detecteren en probeer het opnieuw.  TCP-keepalives kunnen worden gebruikt om te detecteren wanneer de SNAT-poorten niet meer werken omdat ze opnieuw moeten worden toegewezen.

### <a name="idletimeout"></a>Keepalives gebruiken om de uitgaande time-out voor inactiviteit opnieuw in te stellen

Uitgaande verbindingen hebben een time-out van 4 minuten. Deze time-out kan niet worden aangepast. U kunt echter Trans Port (bijvoorbeeld TCP-keepalives) of keepalives op toepassings niveau gebruiken om een niet-actieve stroom te vernieuwen en zo nodig de time-out voor inactiviteit opnieuw in te stellen.  

Bij het gebruik van TCP-keepalives is het voldoende om deze in te scha kelen aan één zijde van de verbinding. Het is bijvoorbeeld voldoende om ze alleen in te scha kelen op de server om de niet-actieve timer van de stroom opnieuw in te stellen en het is niet nodig voor beide zijden voor het starten van TCP-keepalives.  Er bestaan soort gelijke concepten voor Application Layer, waaronder client-server configuraties voor data bases.  Controleer de server zijde voor welke opties er bestaan voor toepassingsspecifieke keepalives.

## <a name="discoveroutbound"></a>Het open bare IP-adres dat door een virtuele machine wordt gebruikt, wordt gedetecteerd
Er zijn veel manieren om het IP-adres van de open bare bron van een uitgaande verbinding te bepalen. OpenDNS biedt een service waarmee u het open bare IP-adres van uw virtuele machine kunt weer geven. 

Met de opdracht nslookup kunt u een DNS-query voor de naam myip.opendns.com verzenden naar de OpenDNS-resolver. De service retourneert het bron-IP-adres dat is gebruikt om de query te verzenden. Wanneer u de volgende query uitvoert vanaf uw virtuele machine, is het antwoord het open bare IP-adres dat voor die virtuele machine wordt gebruikt:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Uitgaande connectiviteit voor komen
Soms is het niet wenselijk dat een virtuele machine een uitgaande stroom maakt. Of er is mogelijk een vereiste om te beheren welke doelen kunnen worden bereikt met uitgaande stromen of welke doelen binnenkomende stromen kunnen starten. In dit geval kunt u [netwerk beveiligings groepen](../virtual-network/security-overview.md) gebruiken om de doelen te beheren die de virtuele machine kan bereiken. U kunt Nsg's ook gebruiken om te beheren welke open bare bestemming binnenkomende stromen kan initiëren.

Wanneer u een NSG toepast op een virtuele machine met taak verdeling, moet u rekening houden met de [service Tags](../virtual-network/security-overview.md#service-tags) en de [standaard beveiligings regels](../virtual-network/security-overview.md#default-security-rules). U moet ervoor zorgen dat de VM Health probe-aanvragen van Azure Load Balancer kan ontvangen. 

Als een NSG de status test aanvragen blokkeert vanuit het standaard label AZURE_LOADBALANCER, mislukt de VM-status test en wordt de virtuele machine gemarkeerd. Load Balancer stopt met het verzenden van nieuwe stromen naar die virtuele machine.

## <a name="limitations"></a>Beperkingen
- Disableoutboundsnat toegevoegd is niet beschikbaar als optie bij het configureren van een taakverdelings regel in de portal.  Gebruik in plaats daarvan REST-, sjabloon-of client hulpprogramma's.
- Rollen van webwerkers zonder VNet en andere micro soft-platform Services kunnen toegankelijk zijn wanneer alleen een interne Standard Load Balancer wordt gebruikt als gevolg van een neven effect van de manier waarop de voor bereide en andere functie van de platform services functioneren. Vertrouw niet op deze zijde als de respectieve service zelf of het onderliggende platform kan zonder kennisgeving worden gewijzigd. U moet altijd aannemen dat u een uitgaande connectiviteit expliciet moet maken als u alleen een interne Standard Load Balancer wilt gebruiken. Het [standaard SNAT](#defaultsnat) -scenario 3 dat in dit artikel wordt beschreven, is niet beschikbaar.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Standard Load Balancer](load-balancer-standard-overview.md).
- Meer informatie over [Uitgaande regels](load-balancer-outbound-rules-overview.md) voor standaard open bare Load Balancer.
- Meer informatie over [Load Balancer](load-balancer-overview.md).
- Meer informatie over [netwerk beveiligings groepen](../virtual-network/security-overview.md).
- Meer informatie over een aantal van de andere belang rijke [netwerk mogelijkheden](../networking/networking-overview.md) van Azure.
