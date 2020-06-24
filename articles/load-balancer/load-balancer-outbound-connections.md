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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 0d16823f920695f84db74122c9a2ac07de0abdb2
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84907071"
---
# <a name="outbound-connections-in-azure"></a>Uitgaande verbindingen in Azure

De Azure Load Balancer biedt een uitgaande verbinding voor klant implementaties via verschillende mechanismen. In dit artikel wordt beschreven wat de scenario's zijn, wanneer deze van toepassing zijn, hoe ze werken en hoe u ze beheert. Als u problemen ondervindt met uitgaande verbindingen via een Azure Load Balancer, raadpleegt u de [hand leiding voor het oplossen van problemen met uitgaande verbindingen](../load-balancer/troubleshoot-outbound-connection.md).

>[!NOTE] 
>In dit artikel worden alleen de implementaties van Resource Manager besproken. Controleer de [uitgaande verbindingen (klassiek)](load-balancer-outbound-connections-classic.md) voor alle klassieke implementatie Scenario's in Azure.

Een implementatie in azure kan communiceren met eind punten buiten Azure in de open bare IP-adres ruimte. Wanneer een exemplaar een uitgaande stroom initieert naar een bestemming in de open bare IP-adres ruimte, wijst Azure het privé-IP-adres dynamisch toe aan een openbaar IP-adres. Nadat deze toewijzing is gemaakt, kan het retour verkeer voor deze uitgaande stroom van het doel ook het privé-IP-adres bereiken waarvan de stroom afkomstig is.

Azure gebruikt bron Network Address Translation (SNAT) om deze functie uit te voeren. Wanneer meerdere privé-IP-adressen worden gemaskeerd achter één openbaar IP-adres, gebruikt Azure [poort adres omzetting (Pat)](#pat) voor het maskeren van privé-IP-adressen. Tijdelijke poorten worden gebruikt voor PAT en zijn [vooraf toegewezen](#preallocatedports) op basis van pool grootte.

Er zijn meerdere [uitgaande scenario's](#scenarios). U kunt deze scenario's zo nodig combi neren. Lees deze zorgvuldig door om inzicht te krijgen in de mogelijkheden, beperkingen en patronen zoals die van toepassing zijn op uw implementatie model en toepassings scenario. Raadpleeg de richt lijnen voor het [beheren van deze scenario's](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

>[!IMPORTANT] 
>Standard Load Balancer en standaard open bare IP introduceren nieuwe mogelijkheden en verschillende gedragingen voor uitgaande connectiviteit.  Ze zijn niet hetzelfde als de basis-Sku's.  Als u een uitgaande verbinding wilt gebruiken bij het werken met standaard-Sku's, moet u deze expliciet definiëren met een openbaar IP-adres of standaard open bare Load Balancer.  Dit omvat het maken van uitgaande connectiviteit bij het gebruik van een interne Standard Load Balancer.  U wordt aangeraden om altijd uitgaande regels te gebruiken op een standaard open bare Load Balancer.  [Scenario 3](#defaultsnat) is niet beschikbaar voor de standaard-SKU.  Dit betekent dat als er een interne Standard Load Balancer wordt gebruikt, u stappen moet nemen om uitgaande connectiviteit te maken voor de virtuele machines in de back-endadresgroep als uitgaande connectiviteit gewenst is.  In de context van uitgaande connectiviteit, één zelfstandige virtuele machine, alle virtuele machines in een Beschikbaarheidsset, werken alle exemplaren in een VMSS als groep. Dit betekent dat als er één virtuele machine in een Beschikbaarheidsset is gekoppeld aan een standaard-SKU, alle VM-exemplaren binnen deze Beschikbaarheidsset nu dezelfde regels gedragen als voor de standaard-SKU, zelfs als een afzonderlijke instantie niet rechtstreeks is gekoppeld. Dit gedrag wordt ook waargenomen in het geval van een zelfstandige virtuele machine met meerdere netwerk interface kaarten die aan een load balancer zijn gekoppeld. Als één NIC als zelfstandige wordt toegevoegd, heeft deze hetzelfde gedrag. Lees dit volledige document aandachtig door om inzicht te krijgen in de algemene concepten, Lees [Standard Load Balancer](load-balancer-standard-overview.md) voor verschillen tussen sku's en controleer de [regels voor uitgaande verbindingen](load-balancer-outbound-rules-overview.md).  Met uitgaande regels kunt u de controle over alle aspecten van de uitgaande connectiviteit nauw keuriger maken.

## <a name="scenario-overview"></a><a name="scenarios"></a>Overzicht van scenario's

Azure Load Balancer en gerelateerde resources worden expliciet gedefinieerd wanneer u [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)gebruikt.  Azure biedt momenteel drie verschillende methoden voor het bezorgen van uitgaande connectiviteit voor Azure Resource Manager-resources. 

| SKU's | Scenario | Methode | IP-protocollen | Beschrijving |
| --- | --- | --- | --- | --- |
| Standaard, basis | [1. VM met een openbaar IP-adres op exemplaar niveau (met of zonder Load Balancer)](#ilpip) | SNAT, poort masker niet gebruikt | TCP, UDP, ICMP, ESP | Azure gebruikt het open bare IP-adres dat is toegewezen aan de IP-configuratie van de NIC van de instantie. Alle tijdelijke poorten zijn beschikbaar voor het exemplaar. Wanneer u Standard Load Balancer gebruikt, worden [Uitgaande regels](load-balancer-outbound-rules-overview.md) niet ondersteund als er een openbaar IP-adres aan de virtuele machine wordt toegewezen. |
| Standaard, basis | [2. open bare Load Balancer gekoppeld aan een virtuele machine (geen openbaar IP-adres op het exemplaar)](#lb) | SNAT met poort maskering (PAT) met behulp van de Load Balancer-frontends | TCP, UDP |Azure deelt het open bare IP-adres van de open bare front Load Balancer-front-end met meerdere privé IP-adressen. Azure gebruikt tijdelijke poorten van de front-ends tot PAT. Wanneer u Standard Load Balancer gebruikt, moet u [Uitgaande regels](load-balancer-outbound-rules-overview.md) gebruiken om de uitgaande connectiviteit expliciet te definiëren. |
| geen of basis | [3. zelfstandige VM (niet Load Balancer, geen openbaar IP-adres)](#defaultsnat) | SNAT met poort maskering (PAT) | TCP, UDP | Azure wijst automatisch een openbaar IP-adres toe voor SNAT, deelt dit open bare IP-adres met meerdere privé IP-adressen van de beschikbaarheidsset en maakt gebruik van tijdelijke poorten van dit open bare IP-adres. Dit scenario is een terugval van de voor gaande scenario's. Het is niet raadzaam om het te controleren en te beheren. |

Als u niet wilt dat een virtuele machine communiceert met eind punten buiten Azure in een open bare IP-adres ruimte, kunt u netwerk beveiligings groepen (Nsg's) gebruiken om de toegang naar behoefte te blok keren. In de sectie wordt voor [komen dat uitgaande connectiviteit](#preventoutbound) nsg's meer details bevat. Richt lijnen voor het ontwerpen, implementeren en beheren van een virtueel netwerk zonder uitgaande toegang valt buiten het bereik van dit artikel.

### <a name="scenario-1-vm-with-public-ip-address"></a><a name="ilpip"></a>Scenario 1: virtuele machine met openbaar IP-adres

In dit scenario wordt er een openbaar IP-adres aan de virtuele machine toegewezen. Voor uitgaande verbindingen is het niet van belang of de virtuele machine gelijkmatig is verdeeld of niet. Dit scenario heeft voor rang op de andere. Wanneer een openbaar IP-adres wordt gebruikt, gebruikt de virtuele machine het open bare IP-adres voor alle uitgaande stromen.  

Een openbaar IP-adres dat is toegewezen aan een virtuele machine is een 1:1-relatie (in plaats van 1: veel) en geïmplementeerd als een stateless 1:1 NAT.  Poort maskering (PAT) wordt niet gebruikt en de virtuele machine heeft alle tijdelijke poorten die beschikbaar zijn voor gebruik.

Als uw toepassing veel uitgaande stromen initieert en u de SNAT-poort uitputtt, kunt u overwegen een [openbaar IP-adres toe te wijzen om de SNAT-beperkingen te beperken](../load-balancer/troubleshoot-outbound-connection.md#assignilpip). Bekijk het beheer van de [SNAT-uitputting](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) in zijn geheel.

### <a name="scenario-2-load-balanced-vm-without-a-public-ip-address"></a><a name="lb"></a>Scenario 2: virtuele machine met taak verdeling zonder openbaar IP-adres

In dit scenario maakt de VM deel uit van een open bare Load Balancer back-end-groep. Aan de virtuele machine is geen openbaar IP-adres toegewezen. De Load Balancer resource moet worden geconfigureerd met een load balancer regel om een koppeling te maken tussen de open bare IP-frontend met de back-end-groep.

Als u deze regel configuratie niet voltooit, is het gedrag zoals beschreven in het scenario voor [zelfstandige virtuele machine zonder openbaar IP-adres](#defaultsnat). Het is niet nodig dat de regel een werkende listener in de back-end-pool heeft om de status test te laten slagen.

Wanneer de virtuele machine met taak verdeling een uitgaande stroom maakt, vertaalt Azure het IP-adres van de particuliere bron van de uitgaande stroom naar het open bare IP-adres van de open bare frontend van Load Balancer. Azure gebruikt SNAT om deze functie uit te voeren. Azure gebruikt ook [Pat](#pat) om meerdere privé-IP-adressen achter een openbaar IP-adres te maskeren. 

Tijdelijke poorten van de open bare IP-adres-frontend van de load balancer worden gebruikt voor het onderscheiden van de afzonderlijke stromen die afkomstig zijn van de virtuele machine. SNAT maakt dynamisch gebruik van [vooraf toegewezen tijdelijke poorten](#preallocatedports) wanneer uitgaande stromen worden gemaakt. In deze context worden de tijdelijke poorten die worden gebruikt voor SNAT de SNAT-poorten genoemd.

SNAT-poorten worden vooraf toegewezen, zoals beschreven in de sectie [over SNAT en Pat](#snat) . Ze zijn een eindige resource die kan worden uitgeput. Het is belang rijk om te begrijpen hoe ze worden [verbruikt](#pat). Als u wilt weten hoe u dit verbruik kunt ontwerpen en zo nodig kunt beperken, raadpleegt u de [beheer SNAT-uitputting](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

Wanneer [meerdere open bare IP-adressen zijn gekoppeld aan Load Balancer Basic](load-balancer-multivip-overview.md), zijn een van deze open bare IP-adressen een kandidaat voor uitgaande stromen en wordt er een wille keurige selectie geselecteerd.  

Als u de status van uitgaande verbindingen met Load Balancer Basic wilt bewaken, kunt u [Azure monitor-Logboeken gebruiken voor Load Balancer](load-balancer-monitor-log.md) en [waarschuwings gebeurtenis logboeken](load-balancer-monitor-log.md#alert-event-log) om te controleren op berichten over SNAT-poort uitputting.

### <a name="scenario-3-standalone-vm-without-a-public-ip-address"></a><a name="defaultsnat"></a>Scenario 3: zelfstandige VM zonder openbaar IP-adres

In dit scenario maakt de virtuele machine geen deel uit van een open bare Load Balancer groep (en maakt geen deel uit van een interne Standard Load Balancer groep) en waaraan geen openbaar IP-adres is toegewezen. Wanneer de virtuele machine een uitgaande stroom maakt, vertaalt Azure het IP-adres van de privé bron van de uitgaande stroom naar een IP-adres van een open bare bron. Het open bare IP-adres dat voor deze uitgaande stroom wordt gebruikt, kan niet worden geconfigureerd en telt niet op basis van de open bare IP-resource limiet van het abonnement. Dit open bare IP-adres behoort niet tot u en kan niet worden gereserveerd. Als u de virtuele machine of Beschikbaarheidsset of de VM-schaalset opnieuw implementeert, wordt dit open bare IP-adres vrijgegeven en wordt er een nieuw openbaar IP-adres aangevraagd. Gebruik dit scenario niet voor White List IP-adressen. Gebruik in plaats daarvan een van de andere twee scenario's waarin u het uitgaande scenario expliciet declareert en het open bare IP-adres dat moet worden gebruikt voor uitgaande verbindingen.

>[!IMPORTANT] 
>Dit scenario is ook van toepassing wanneer __alleen__ een interne basis Load Balancer is gekoppeld. Scenario 3 is __niet beschikbaar__ wanneer een interne Standard Load Balancer is gekoppeld aan een virtuele machine.  U moet een [scenario 1](#ilpip) of [scenario 2](#lb) expliciet maken naast het gebruik van een interne Standard Load Balancer.

Azure gebruikt SNAT met poort maskering ([Pat](#pat)) om deze functie uit te voeren. Dit scenario is vergelijkbaar met [scenario 2](#lb), maar er is geen controle over het gebruikte IP-adres. Dit is een terugval scenario voor situaties waarin de scenario's 1 en 2 niet bestaan. Dit scenario wordt niet aanbevolen als u het uitgaande adres wilt beheren. Als uitgaande verbindingen een belang rijk onderdeel zijn van uw toepassing, moet u een ander scenario kiezen.

SNAT-poorten zijn vooraf toegewezen, zoals beschreven in de sectie [over SNAT en Pat](#snat) .  Het aantal Vm's dat een Beschikbaarheidsset deelt, bepaalt welke voortoewijzings laag van toepassing is.  Een zelfstandige virtuele machine zonder een Beschikbaarheidsset is in feite een pool van 1 voor het bepalen van de voor toewijzing (1024 SNAT-poorten). SNAT-poorten zijn een eindige resource die kan worden uitgeput. Het is belang rijk om te begrijpen hoe ze worden [verbruikt](#pat). Als u wilt weten hoe u dit verbruik kunt ontwerpen en zo nodig kunt beperken, raadpleegt u de [beheer SNAT-uitputting](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

### <a name="multiple-combined-scenarios"></a><a name="combinations"></a>Meerdere, gecombineerde scenario's

U kunt de scenario's die in de voor gaande secties worden beschreven, combi neren om een bepaald resultaat te krijgen. Wanneer er meerdere scenario's aanwezig zijn, geldt een volg orde van prioriteit: [scenario 1](#ilpip) heeft voor rang op [scenario 2](#lb) en [3](#defaultsnat). [Scenario 2](#lb) overschrijft [scenario 3](#defaultsnat).

Een voor beeld is een Azure Resource Manager-implementatie waarbij de toepassing intensief gebruikmaakt van uitgaande verbindingen met een beperkt aantal doelen, maar ook inkomende stromen ontvangt via een Load Balancer front-end. In dit geval kunt u scenario 1 en 2 combi neren voor vrijs telling. Zie voor aanvullende patronen [beheer van SNAT-uitputting](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust).

### <a name="multiple-frontends-for-outbound-flows"></a><a name="multife"></a>Meerdere frontends voor uitgaande stromen

#### <a name="standard-load-balancer"></a>Standard Load Balancer

Standard Load Balancer gebruikt alle kandidaten voor uitgaande stromen tegelijk wanneer [meerdere (open bare) IP-frontends](load-balancer-multivip-overview.md) aanwezig zijn. Elke frontend vermenigvuldigt het aantal beschik bare, vooraf toegewezen SNAT-poorten als een taakverdelings regel is ingeschakeld voor uitgaande verbindingen.

U kunt ervoor kiezen om een frontend-IP-adres dat wordt gebruikt voor uitgaande verbindingen, te onderdrukken met een nieuwe regel optie voor taak verdeling:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Normaal gesp roken wordt de `disableOutboundSnat` optie standaard ingesteld op _False_ en geeft dit aan dat deze regel verloopt uitgaande SNAT voor de gekoppelde virtuele machines in de back-end-pool van de taakverdelings regel. De `disableOutboundSnat` kan worden gewijzigd in _True_ om te voor komen dat Load Balancer het gekoppelde frontend-IP-adres gebruikt voor uitgaande verbindingen voor de virtuele machines in de back-endserver van deze taakverdelings regel.  Daarnaast kunt u ook nog steeds een specifiek IP-adres voor uitgaande stromen opgeven, zoals wordt beschreven in [meerdere, gecombineerde scenario's](#combinations) .

#### <a name="load-balancer-basic"></a>Load Balancer Basic

Load Balancer Basic kiest voor een eenmalige front-end voor uitgaande stromen wanneer [meerdere (open bare) IP-frontends](load-balancer-multivip-overview.md) kandidaten voor uitgaande stromen zijn. Deze selectie kan niet worden geconfigureerd. u kunt het selectie algoritme het beste wille keurig beschouwen. U kunt een specifiek IP-adres voor uitgaande stromen opgeven, zoals beschreven in [meerdere, gecombineerde scenario's](#combinations).

### <a name="availability-zones"></a><a name="az"></a>Beschikbaarheidszones

Wanneer u [Standard Load Balancer met Beschikbaarheidszones](load-balancer-standard-availability-zones.md)gebruikt, kan de zone-redundante front-ends zone-redundante uitgaande SNAT-verbindingen bieden en kan er met de SNAT-programmering een zone storing optreden.  Wanneer zonegebonden-front-ends worden gebruikt, delen uitgaande SNAT-verbindingen verspreiding met de zone waartoe ze behoren.

## <a name="understanding-snat-and-pat"></a><a name="snat"></a>Meer informatie over SNAT en PAT

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Poort maskering SNAT (PAT)

Wanneer een open bare Load Balancer resource is gekoppeld aan VM-exemplaren, die geen toegewezen open bare IP-adressen hebben, wordt elke uitgaande verbindings bron herschreven. De bron wordt herschreven van de privé-IP-adres ruimte van het virtuele netwerk naar het open bare IP-adres van de load balancer. In de open bare IP-adres ruimte moet de 5-tuple van de stroom (bron-IP-adres, bron poort, IP-transport protocol, doel-IP-adres, doel poort) uniek zijn. Het SNAT-poort masker kan worden gebruikt met TCP-of UDP IP-protocollen.

Tijdelijke poorten (SNAT-poorten) worden gebruikt om dit te verhelpen na het herschrijven van het IP-adres van de privé bron, omdat meerdere stromen afkomstig zijn van één openbaar IP-adres. Met het SNAT-algoritme voor poort maskering worden de SNAT-poorten anders toegewezen voor UDP versus TCP.

#### <a name="tcp-snat-ports"></a><a name="tcp"></a>TCP-SNAT-poorten

Eén SNAT-poort wordt per stroom verbruikt naar één doel-IP-adres, poort. Voor meerdere TCP-stromen naar hetzelfde doel-IP-adres, dezelfde poort en hetzelfde protocol gebruikt elke TCP-stroom één SNAT-poort. Dit zorgt ervoor dat de stromen uniek zijn wanneer ze afkomstig zijn uit hetzelfde open bare IP-adres en naar hetzelfde doel-IP-adres, dezelfde poort en hetzelfde protocol gaan. 

Meerdere stromen, elk naar een ander doel-IP-adres, poort en protocol, delen één SNAT-poort. Het doel-IP-adres, de poort en het Protocol maken stroom uniek zonder dat er extra bron poorten nodig zijn om stromen te onderscheiden van de open bare IP-adres ruimte.

#### <a name="udp-snat-ports"></a><a name="udp"></a>UDP SNAT-poorten

UDP SNAT-poorten worden beheerd door een ander algoritme dan TCP-SNAT-poorten.  Load Balancer gebruikt een algoritme dat wordt aangeduid als ' Port-restricted kegel NAT ' voor UDP.  Er wordt één SNAT-poort verbruikt voor elke stroom, ongeacht het doel-IP-adres, de poort.

#### <a name="snat-port-reuse"></a>SNAT-poort opnieuw gebruiken

Zodra een poort is vrijgegeven, kan de poort zo nodig opnieuw worden gebruikt.  U kunt SNAT-poorten beschouwen als een reeks van laag naar Maxi maal beschikbaar voor een bepaald scenario en de eerste beschik bare SNAT-poort wordt gebruikt voor nieuwe verbindingen. 
 
#### <a name="exhaustion"></a>Uitputting

Wanneer de SNAT-poort resources zijn uitgeput, mislukken uitgaande stromen totdat bestaande stromen van SNAT-poorten worden vrijgegeven. Load Balancer overschrijden de SNAT-poorten wanneer de stroom wordt gesloten en gebruikt een [time-out van 4 minuten](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) voor het vrijmaken van de SNAT-poorten van niet-actieve stromen.

UDP SNAT-poorten lopen doorgaans veel sneller dan TCP SNAT-poorten vanwege het verschil in gebruikte algoritmen. U moet de test met dit verschil ontwerpen en schalen.

Raadpleeg de sectie [SNAT beheren](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) als u patronen wilt beperken die vaak leiden tot een SNAT-poort uitputting.

### <a name="ephemeral-port-preallocation-for-port-masquerading-snat-pat"></a><a name="preallocatedports"></a>Tijdelijke poort voortoewijzing voor poort maskering SNAT (PAT)

Azure gebruikt een algoritme om te bepalen hoeveel vooraf toegewezen SNAT-poorten beschikbaar zijn op basis van de grootte van de back-end-pool wanneer poort maskering SNAT ([Pat](#pat)) wordt gebruikt. SNAT-poorten zijn tijdelijke poorten die beschikbaar zijn voor een bepaald openbaar IP-bron adres. Voor elk open bare IP-adres dat is gekoppeld aan een load balancer zijn er 64.000 poorten beschikbaar als SNAT-poorten voor elk IP-transport protocol.

Hetzelfde aantal SNAT-poorten wordt vooraf toegewezen voor UDP en TCP, respectievelijk onafhankelijk per IP-transport protocol.  Het SNAT-poort gebruik is echter anders, afhankelijk van het feit of de stroom UDP of TCP is.

>[!IMPORTANT]
>Standaard-SKU SNAT-programmering is per IP-transport protocol en afgeleid van de taakverdelings regel.  Als er slechts een regel voor TCP-taak verdeling bestaat, is SNAT alleen beschikbaar voor TCP. Als u alleen een TCP-taakverdelings regel hebt en uitgaand SNAT voor UDP nodig hebt, maakt u een UDP-taakverdelings regel van dezelfde frontend naar dezelfde back-end-groep.  Hiermee wordt SNAT-programmering geactiveerd voor UDP.  Een werk regel of status test is niet vereist.  Basic SKU SNAT always Program ma's SNAT voor IP-transport protocol, ongeacht het transport protocol dat is opgegeven in de taakverdelings regel.

Azure wijst SNAT-poorten vooraf toe aan de IP-configuratie van de NIC van elke virtuele machine. Wanneer een IP-configuratie wordt toegevoegd aan de pool, worden de SNAT-poorten vooraf toegewezen voor deze IP-configuratie op basis van de grootte van de back-endserver. Wanneer uitgaande stromen worden gemaakt, gebruikt [Pat](#pat) dynamisch (Maxi maal de vooraf toegewezen limiet) en worden deze poorten vrijgegeven wanneer de stroom wordt gesloten of er [time-outs voor inactiviteit](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) optreden.

In de volgende tabel ziet u de SNAT-poort voortoewijzingen voor lagen van back-endadresgroep-Pools:

| Pool grootte (VM-exemplaren) | Vooraf toegewezen SNAT-poorten per IP-configuratie|
| --- | --- |
| 1-50 | 1.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

>[!NOTE]
> Wanneer u Standard Load Balancer met [meerdere frontends](load-balancer-multivip-overview.md)gebruikt, vermenigvuldigt elk frontend-IP-adres het aantal beschik bare SNAT-poorten in de vorige tabel. Bijvoorbeeld, een back-end-pool van 50 VM met 2 taakverdelings regels, elk met een afzonderlijk frontend-IP-adres, zal 2048 (2x 1024) SNAT-poorten per regel gebruiken. Bekijk de Details voor [meerdere frontends](#multife).

Houd er rekening mee dat het aantal beschik bare SNAT-poorten niet rechtstreeks naar het aantal stromen kan worden vertaald. Eén SNAT-poort kan opnieuw worden gebruikt voor meerdere unieke bestemmingen. Poorten worden alleen gebruikt als het nodig is om stromen uniek te maken. Raadpleeg de sectie over het [beheren van deze exhaustible-resource](../load-balancer/troubleshoot-outbound-connection.md#snatexhaust) en de sectie met [Pat](#pat)voor instructies voor het ontwerpen en beperken van problemen.

Het wijzigen van de grootte van uw back-end-pool kan invloed hebben op sommige van uw vastgelegde stromen. Als de grootte van de back-end-groep toeneemt en overschakelt naar de volgende laag, worden de helft van uw vooraf toegewezen SNAT-poorten vrijgemaakt tijdens de overgang naar de volgende grotere back-endadresgroep. Stromen die zijn gekoppeld aan een vrijgestelde SNAT-poort, worden getimed en moeten opnieuw worden ingesteld. Als er een nieuwe stroom wordt gedaan, wordt de stroom onmiddellijk uitgevoerd zolang er vooraf toegewezen poorten beschikbaar zijn.

Als de back-end-pool grootte vermindert en overgangen naar een lagere laag, neemt het aantal beschik bare SNAT-poorten toe. In dit geval worden bestaande toegewezen SNAT-poorten en de bijbehorende stromen niet beïnvloed.

Toewijzing van SNAT-poorten is een specifiek IP-transport protocol (TCP en UDP worden afzonderlijk onderhouden) en worden vrijgegeven onder de volgende voor waarden:

### <a name="tcp-snat-port-release"></a>TCP SNAT-poort release

- Als de server/client FINACK verzendt, wordt de SNAT-poort na 240 seconden vrijgegeven.
- Als een RST wordt gezien, wordt de SNAT-poort na 15 seconden vrijgegeven.
- Als er een time-out voor inactiviteit is bereikt, wordt de poort vrijgegeven.

### <a name="udp-snat-port-release"></a>UDP SNAT-poort release

- Als er een time-out voor inactiviteit is bereikt, wordt de poort vrijgegeven.

## <a name="discovering-the-public-ip-that-a-vm-uses"></a><a name="discoveroutbound"></a>Het open bare IP-adres dat door een virtuele machine wordt gebruikt, wordt gedetecteerd
Er zijn veel manieren om het IP-adres van de open bare bron van een uitgaande verbinding te bepalen. OpenDNS biedt een service waarmee u het open bare IP-adres van uw virtuele machine kunt weer geven. 

Met de opdracht nslookup kunt u een DNS-query voor de naam myip.opendns.com verzenden naar de OpenDNS-resolver. De service retourneert het bron-IP-adres dat is gebruikt om de query te verzenden. Wanneer u de volgende query uitvoert vanaf uw virtuele machine, is het antwoord het open bare IP-adres dat voor die virtuele machine wordt gebruikt:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Uitgaande connectiviteit voor komen
Soms is het niet wenselijk dat een virtuele machine een uitgaande stroom maakt. Of er is mogelijk een vereiste om te beheren welke doelen kunnen worden bereikt met uitgaande stromen of welke doelen binnenkomende stromen kunnen starten. In dit geval kunt u [netwerk beveiligings groepen](../virtual-network/security-overview.md) gebruiken om de doelen te beheren die de virtuele machine kan bereiken. U kunt Nsg's ook gebruiken om te beheren welke open bare bestemming binnenkomende stromen kan initiëren.

Wanneer u een NSG toepast op een virtuele machine met taak verdeling, moet u rekening houden met de [service Tags](../virtual-network/security-overview.md#service-tags) en de [standaard beveiligings regels](../virtual-network/security-overview.md#default-security-rules). U moet ervoor zorgen dat de VM Health probe-aanvragen van Azure Load Balancer kan ontvangen. 

Als een NSG de status test aanvragen blokkeert vanuit het standaard label AZURE_LOADBALANCER, mislukt de VM-status test en wordt de virtuele machine gemarkeerd. Load Balancer stopt met het verzenden van nieuwe stromen naar die virtuele machine.

## <a name="connections-to-azure-storage-in-the-same-region"></a>Verbindingen met Azure Storage in dezelfde regio

Het is niet nodig om verbinding te maken met opslag in dezelfde regio als de virtuele machine als u uitgaande verbindingen hebt via de bovenstaande scenario's. Als u dit niet wilt, gebruikt u netwerk beveiligings groepen (Nsg's) zoals hierboven is uitgelegd. Voor connectiviteit met opslag in andere regio's is uitgaande connectiviteit vereist. Houd er rekening mee dat wanneer u verbinding maakt met de opslag van een virtuele machine in dezelfde regio, het bron-IP-adres in de logboeken voor diagnostische gegevens van de opslag locatie van de interne provider is en niet het open bare IP-adres van uw virtuele machine. Als u de toegang tot uw opslag account wilt beperken tot Vm's in een of meer Virtual Network subnetten in dezelfde regio, gebruikt u [Virtual Network Service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md) en niet uw open bare IP-adres bij het configureren van de firewall van uw opslag account. Zodra service-eind punten zijn geconfigureerd, ziet u uw Virtual Network privé-IP-adres in uw logboeken voor opslag diagnose en niet het adres van de interne provider.

## <a name="azure-load-balancer-outbound-rules"></a><a name="outboundrules"></a>Azure Load Balancer uitgaande regels

Azure Load Balancer biedt een uitgaande verbinding van een virtueel netwerk naast binnenkomend.  Met uitgaande regels kunt u de uitgaande Network Address Translation van open bare [Standard Load Balancer](load-balancer-standard-overview.md)eenvoudig configureren.  U hebt volledige declaratieve controle over uitgaande connectiviteit om deze mogelijkheid te schalen en af te stemmen op uw specifieke behoeften.

![Load Balancer uitgaande regels](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Met uitgaande regels kunt u Load Balancer gebruiken voor het volgende: 
- Geef de uitgaande NAT van een geheel nieuwe definitie op.
- het gedrag van bestaande uitgaande NAT schalen en afstemmen. 

Met uitgaande regels kunt u het volgende beheren:
- welke virtuele machines moeten worden vertaald naar de open bare IP-adressen. 
- hoe [uitgaande SNAT-poorten](load-balancer-outbound-connections.md#snat) moeten worden toegewezen.
- de protocollen waarvoor een uitgaande vertaling moet worden geboden.
- welke duur moet worden gebruikt voor de time-out voor uitgaande verbindingen (4-120 minuten).
- of een TCP-Reset moet worden verzonden bij een time-out voor inactiviteit

Uitgaande regels worden [scenario 2](load-balancer-outbound-connections.md#lb) uitvouwen in de beschrijving in het artikel [uitgaande verbindingen](load-balancer-outbound-connections.md) en de prioriteit van het scenario blijft ongewijzigd.

### <a name="outbound-rule"></a>Uitgaande regel

Net als alle Load Balancer regels volgen uitgaande regels dezelfde vertrouwde syntaxis als taak verdeling en binnenkomende NAT-regels:

Front- **End**  +  **para meters**  +  **back-end-groep**

Met een uitgaande regel wordt uitgaande NAT geconfigureerd voor _alle virtuele machines die door de back-endadresgroep worden geïdentificeerd_ om te worden vertaald naar de front- _End_.  En _para meters_ bieden extra nauw keurige controle over de uitgaande NAT-algoritme.

API-versie 2018-07-01 geeft de volgende structuur van een uitgaande regel:

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>De effectief uitgaande NAT-configuratie bestaat uit een combi natie van alle regels voor uitgaande verbindingen en taak verdeling. Uitgaande regels zijn incrementeel voor taakverdelings regels. Bekijk het [uitschakelen van de uitgaande NAT voor een taakverdelings regel](#disablesnat) voor het beheren van de effectief uitgaande NAT-omzetting wanneer meerdere regels van toepassing zijn op een virtuele machine. U moet [uitgaande SNAT uitschakelen](#disablesnat) bij het definiëren van een uitgaande regel die hetzelfde open bare IP-adres gebruikt als een taakverdelings regel.

#### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>Uitgaand NAT schalen met meerdere IP-adressen

Terwijl een regel voor uitgaande verbindingen met slechts één openbaar IP-adres kan worden gebruikt, wordt door uitgaande regels de configuratie belasting voor het schalen van uitgaande NAT vereenvoudigd. U kunt meerdere IP-adressen gebruiken om te plannen voor grootschalige scenario's en u kunt uitgaande regels gebruiken om te voor komen dat er gevoelige patronen voor de [uitputting van SNAT](troubleshoot-outbound-connection.md#snatexhaust) zijn.  

Elk extra IP-adres dat wordt geleverd door een front-end biedt 64.000 tijdelijke poorten voor Load Balancer die als SNAT-poorten kunnen worden gebruikt. Terwijl taak verdeling of binnenkomende NAT-regels één frontend hebben, breidt de uitgaande regel het front-end-principe uit en staat meerdere frontends per regel toe.  Met meerdere frontends per regel wordt het aantal beschik bare SNAT-poorten vermenigvuldigd met elk openbaar IP-adres en kunnen grote scenario's worden ondersteund.

Daarnaast kunt u een [openbaar IP-voor voegsel](https://aka.ms/lbpublicipprefix) rechtstreeks met een uitgaande regel gebruiken.  Met behulp van het voor voegsel openbaar IP-adres kunt u eenvoudig schalen en vereenvoudigd wit-overzicht van stromen die afkomstig zijn van uw Azure-implementatie. U kunt een front-end-IP-configuratie binnen de Load Balancer resource configureren om rechtstreeks naar een open bare IP-adres voorvoegsel te verwijzen.  Hierdoor is Load Balancer exclusieve controle over het open bare IP-voor voegsel en worden alle open bare IP-adressen die zich in het open bare IP-voor voegsel voor uitgaande verbindingen bevinden, automatisch door de uitgaande regel gebruikt.  Elk van de IP-adressen binnen het bereik van het open bare IP-voor voegsel bieden 64.000 tijdelijke poorten per IP-adres om Load Balancer te gebruiken als SNAT-poorten.   

U kunt geen afzonderlijke open bare IP-adres bronnen maken op basis van het open bare IP-voor voegsel wanneer u deze optie gebruikt, omdat de uitgaande regel volledige controle over het open bare IP-voor voegsel moet hebben.  Als u meer nauw keurigere controle nodig hebt, kunt u een afzonderlijke open bare IP-adres bron maken op basis van het open bare IP-voor voegsel en meerdere open bare IP-adressen afzonderlijk aan de frontend van een uitgaande regel toewijzen.

#### <a name="tune-snat-port-allocation"></a><a name="snatports"></a>Toewijzing van SNAT-poort afstemmen

U kunt uitgaande regels gebruiken voor het afstemmen [van de automatische toewijzing van de SNAT-poort op basis van de back-endadresgroep](load-balancer-outbound-connections.md#preallocatedports) en toewijzen van meer of minder dan de automatische toewijzing van de SNAT-poort.

Gebruik de volgende para meter om 10.000 SNAT-poorten per virtuele machine toe te wijzen (NIC IP-configuratie).
 

          "allocatedOutboundPorts": 10000

Elk openbaar IP-adres van alle frontends van een uitgaande regel draagt bij aan Maxi maal 64.000 tijdelijke poorten voor gebruik als SNAT-poorten.  Load Balancer wijst SNAT-poorten toe in veelvouden van 8. Als u een waarde opgeeft die niet deelbaar is door 8, wordt de configuratie bewerking geweigerd.  Als u meer SNAT-poorten probeert toe te wijzen dan beschikbaar zijn op basis van het aantal open bare IP-adressen, wordt de configuratie bewerking geweigerd.  Als u bijvoorbeeld 10.000 poorten per VM toewijst en 7 virtuele machines in een back-end-pool een enkel openbaar IP-adres delen, wordt de configuratie geweigerd (7 x 10.000 SNAT-poorten > 64.000 SNAT-poorten).  U kunt meer open bare IP-adressen toevoegen aan de front-end van de regel voor uitgaande verbindingen om het scenario in te scha kelen.

U kunt teruggaan naar de [automatische toewijzing van de SNAT-poort op basis van de grootte](load-balancer-outbound-connections.md#preallocatedports) van de back-endadresgroep door 0 op te geven voor het aantal poorten. In dat geval krijgen de eerste 50 VM-exemplaren 1024 poorten, worden 51-100 VM-exemplaren 512 en dus weer op basis van de tabel.

#### <a name="control-outbound-flow-idle-timeout"></a><a name="idletimeout"></a>Uitgaande time-out voor niet-actieve stroom controleren

Uitgaande regels bieden een configuratie parameter voor het beheren van de uitgaande time-out voor niet-actieve stroom en om deze te vergelijken met de behoeften van uw toepassing.  Uitgaande time-outs voor inactiviteit zijn standaard ingesteld op 4 minuten.  De para meter accepteert een waarde van 4 tot 120 tot en met een specifiek aantal minuten voor de time-out voor inactiviteit voor stromen die overeenkomen met deze specifieke regel.

Gebruik de volgende para meter om de uitgaande time-out voor inactiviteit in te stellen op 1 uur:

          "idleTimeoutInMinutes": 60

#### <a name="enable-tcp-reset-on-idle-timeout"></a><a name="tcprst"></a><a name="tcpreset"></a>TCP Reset inschakelen bij time-out inactiviteit

Het standaard gedrag van Load Balancer is om de stroom op de achtergrond neer te zetten wanneer de uitgaande time-out voor inactiviteit is bereikt.  Met de para meter enableTCPReset kunt u een meer voorspel bare toepassings gedrag inschakelen en bepalen of u een bidirectionele TCP Reset (TCP RST) wilt verzenden wanneer de uitgaande time-out voor inactiviteit is verlopen. 

Gebruik de volgende para meter om TCP reset in te scha kelen voor een uitgaande regel:

           "enableTcpReset": true

[TCP Reset controleren bij time-out voor inactiviteit](https://aka.ms/lbtcpreset) voor meer informatie over de beschik baarheid van regio's.

#### <a name="support-both-tcp-and-udp-transport-protocols-with-a-single-rule"></a><a name="proto"></a>Ondersteuning voor TCP-en UDP-transport protocollen met één regel

Waarschijnlijk wilt u ' all ' gebruiken voor het transport protocol van de uitgaande regel, maar u kunt ook de uitgaande regel Toep assen op een specifiek Transport Protocol, ook als dat nodig is.

Gebruik de volgende para meter om het protocol in te stellen op TCP en UDP:

          "protocol": "All"

#### <a name="disable-outbound-nat-for-a-load-balancing-rule"></a><a name="disablesnat"></a>Uitgaande NAT uitschakelen voor een taakverdelings regel

Zoals eerder vermeld, bieden de taakverdelings regels automatische programmering van uitgaande NAT. Sommige scenario's profiteren echter of vereisen dat u de automatische programmering van uitgaande NAT door de taakverdelings regel uitschakelt, zodat u het gedrag kunt beheren of verfijnen.  Uitgaande regels hebben scenario's waarbij het belang rijk is om de automatische uitgaande NAT-programmering te stoppen.

U kunt deze para meter op twee manieren gebruiken:
- Optionele onderdrukking van het gebruik van het inkomende IP-adres voor uitgaande NAT.  Uitgaande regels zijn incrementeel voor taakverdelings regels en met deze parameterset, wordt de regel voor uitgaande verbindingen in beheer.
  
- Stem de uitgaande NAT-para meters af van een IP-adres dat tegelijkertijd wordt gebruikt voor inkomend en uitgaand.  De automatische uitgaand NAT-programmering moet worden uitgeschakeld zodat een regel voor uitgaand verkeer kan worden beheerd.  Als u bijvoorbeeld de toewijzing van de SNAT-poort van een adres wilt wijzigen, moet deze para meter worden ingesteld op True.  Als u probeert een regel voor uitgaande verbindingen te gebruiken om de para meters van een IP-adres dat ook wordt gebruikt voor inkomend verkeer opnieuw te definiëren en geen uitgaande NAT-programmering van de taakverdelings regel heeft vrijgegeven, mislukt de bewerking voor het configureren van een uitgaande regel.

>[!IMPORTANT]
> De virtuele machine heeft geen uitgaande verbinding als u deze para meter instelt op True en geen uitgaande regel (of [openbaar IP-scenario op exemplaar niveau](load-balancer-outbound-connections.md#ilpip) hebt om uitgaande connectiviteit te definiëren.  Sommige bewerkingen van uw virtuele machine of uw toepassing kunnen afhankelijk zijn van een uitgaande verbinding. Zorg ervoor dat u bekend bent met de afhankelijkheden van uw scenario en dat dit gevolgen heeft voor het maken van deze wijziging.

U kunt uitgaande SNAT uitschakelen op de taakverdelings regel met deze configuratie parameter:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

De para meter Disableoutboundsnat toegevoegd **wordt** standaard ingesteld op False, wat betekent dat de taakverdelings regel automatische uitgaande NAT biedt als spiegel beeld van de configuratie van de taakverdelings regel.  

Als u Disableoutboundsnat toegevoegd instelt op True voor de taakverdelings regel, geeft de taakverdelings regel de controle over de andere automatische uitgaande NAT-programmering.  Uitgaande SNAT als gevolg van de taakverdelings regel is uitgeschakeld.

#### <a name="reuse-existing-or-define-new-backend-pools"></a>Bestaande back-end-Pools opnieuw gebruiken of definiëren

Uitgaande regels introduceren geen nieuw concept voor het definiëren van de groep Vm's waarop de regel van toepassing moet zijn.  In plaats daarvan wordt het concept van een back-end-pool hergebruikt, dat ook wordt gebruikt voor taakverdelings regels. U kunt dit gebruiken om de configuratie te vereenvoudigen door een bestaande back-end-groeps definitie opnieuw te gebruiken of een specifiek voor een uitgaande regel te maken.

### <a name="scenarios"></a>Scenario's

#### <a name="groom-outbound-connections-to-a-specific-set-of-public-ip-addresses"></a><a name="groom"></a>Uitgaande verbindingen met een specifieke set open bare IP-adressen opschonen

U kunt een uitgaande regel voor het opschonen van uitgaande verbindingen gebruiken om aan te geven dat ze afkomstig zijn uit een specifieke set open bare IP-adressen om white list-scenario's te vereenvoudigen.  Het open bare IP-adres van de bron kan hetzelfde zijn als het gebruik van een taakverdelings regel of een andere set open bare IP-adressen dan wordt gebruikt door een taakverdelings regel.  

1. [Openbaar IP-voor voegsel](https://aka.ms/lbpublicipprefix) (of open bare IP-adressen van open bare IP-voor voegsel) maken
2. Een openbare Standard Load Balancer maken
3. Front-end maken die verwijst naar het open bare IP-voor voegsel (of open bare IP-adressen) die u wilt gebruiken
4. Hergebruik een back-end-pool of maak een back-end-pool en plaats de virtuele machines in een back-end-groep van de open bare Load Balancer
5. Een uitgaande regel voor de open bare Load Balancer configureren voor het Program meren van uitgaande NAT voor deze Vm's met de frontends
   
Als u niet wilt dat de taakverdelings regel voor uitgaand verkeer wordt gebruikt, moet u [uitgaand SNAT uitschakelen](#disablesnat) op de taakverdelings regel.

#### <a name="modify-snat-port-allocation"></a><a name="modifysnat"></a>Toewijzing van SNAT-poort wijzigen

U kunt uitgaande regels gebruiken voor het afstemmen van de [automatische toewijzing van de SNAT-poort op basis van de back-endadresgroep](load-balancer-outbound-connections.md#preallocatedports).

Als u bijvoorbeeld twee virtuele machines hebt die één openbaar IP-adres voor uitgaande NAT delen, kunt u het aantal toegewezen SNAT-poorten van de standaard 1024-poorten verhogen als u de SNAT-uitputting ondervindt. Elk openbaar IP-adres kan bijdragen aan Maxi maal 64.000 tijdelijke poorten.  Als u een uitgaande regel configureert met één openbaar IP-adres front-end, kunt u een totaal van 64.000 SNAT-poorten distribueren naar Vm's in de back-end-pool.  Voor twee virtuele machines kunnen Maxi maal 32.000 SNAT-poorten worden toegewezen met een uitgaande regel (2x 32.000 = 64.000).

Controleer de [uitgaande verbindingen](load-balancer-outbound-connections.md) en de details over de manier waarop de [SNAT](load-balancer-outbound-connections.md#snat) -poorten worden toegewezen en gebruikt.

#### <a name="enable-outbound-only"></a><a name="outboundonly"></a>Alleen uitgaand verkeer inschakelen

U kunt een open bare Standard Load Balancer gebruiken om uitgaande NAT te bieden voor een groep Vm's. In dit scenario kunt u zelf een regel voor uitgaande verbindingen gebruiken, zonder dat u extra regels nodig hebt.

##### <a name="outbound-nat-for-vms-only-no-inbound"></a>Alleen uitgaand NAT voor Vm's (geen inkomend)

Definieer een open bare Standard Load Balancer, plaats de virtuele machines in de back-end-pool en configureer een uitgaande regel voor het Program meren van uitgaande NAT en schoon de uitgaande verbindingen op die afkomstig zijn van een specifiek openbaar IP-adres. U kunt ook een openbaar IP-voor voegsel gebruiken dat is vereenvoudigd wit: de bron van uitgaande verbindingen weer geven.

1. Maak een open bare Standard Load Balancer.
2. Maak een back-end-pool en plaats de virtuele machines in een back-end-groep van de open bare Load Balancer.
3. Een uitgaande regel configureren op de open bare Load Balancer om uitgaande NAT voor deze Vm's te Program meren.

##### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Uitgaande NAT voor interne Standard Load Balancer scenario's

Wanneer u een interne Standard Load Balancer gebruikt, is uitgaand NAT niet beschikbaar totdat de uitgaande connectiviteit expliciet is gedeclareerd. Met de volgende stappen kunt u uitgaande connectiviteit definiëren met behulp van een uitgaande regel voor het maken van uitgaande connectiviteit voor Vm's achter een interne Standard Load Balancer:

1. Maak een open bare Standard Load Balancer.
2. Maak een back-end-pool en plaats de virtuele machines in een back-end-groep van de open bare Load Balancer naast de interne Load Balancer.
3. Een uitgaande regel configureren op de open bare Load Balancer om uitgaande NAT voor deze Vm's te Program meren.

##### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>TCP & UDP-protocollen inschakelen voor uitgaande NAT met een openbaar Standard Load Balancer

- Wanneer u een open bare Standard Load Balancer gebruikt, komt de automatische uitgaande NAT-programmering die overeenkomt met het transport protocol van de taakverdelings regel.  

   1. Schakel uitgaande SNAT uit op de taakverdelings regel.
   2. Een uitgaande regel configureren op hetzelfde Load Balancer.
   3. Gebruik de back-end-groep die al door uw virtuele machines wordt gebruikt.
   4. Geef ' Protocol ': ' all ' op als onderdeel van de uitgaande regel.

- Wanneer alleen binnenkomende NAT-regels worden gebruikt, wordt er geen uitgaand NAT-verkeer gegeven.

   1. Virtuele machines in een back-end-groep plaatsen.
   2. Definieer een of meer frontend-IP-configuraties met een openbaar IP-adres (sen) of een openbaar IP-voor voegsel.
   3. Een uitgaande regel configureren op hetzelfde Load Balancer.
   4. Geef ' Protocol ': ' all ' op als onderdeel van de uitgaande regel


## <a name="limitations"></a>Beperkingen
- Het maximale aantal bruikbare tijdelijke poorten per frontend-IP-adres is 64.000.
- Het bereik van de Configureer bare uitgaande time-out voor inactiviteit is 4 tot 120 minuten (240 tot 7200 seconden).
- Load Balancer biedt geen ondersteuning voor ICMP voor uitgaande NAT.
- Uitgaande regels kunnen alleen worden toegepast op de primaire IP-configuratie van een NIC.  Er worden meerdere Nic's ondersteund.
- Rollen van webwerkers zonder VNet en andere micro soft-platform Services kunnen toegankelijk zijn wanneer alleen een interne Standard Load Balancer wordt gebruikt als gevolg van een neven effect van de manier waarop de voor bereide en andere functie van de platform services functioneren. Vertrouw niet op deze zijde als de respectieve service zelf of het onderliggende platform kan zonder kennisgeving worden gewijzigd. U moet altijd aannemen dat u een uitgaande connectiviteit expliciet moet maken als u alleen een interne Standard Load Balancer wilt gebruiken. Het [standaard SNAT](#defaultsnat) -scenario 3 dat in dit artikel wordt beschreven, is niet beschikbaar.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Standard Load Balancer](load-balancer-standard-overview.md).
- Meer informatie over [Uitgaande regels](load-balancer-outbound-rules-overview.md) voor standaard open bare Load Balancer.
- Meer informatie over [Load Balancer](load-balancer-overview.md).
- Meer informatie over [netwerk beveiligings groepen](../virtual-network/security-overview.md).
- Meer informatie over een aantal van de andere belang rijke [netwerk mogelijkheden](../networking/networking-overview.md) van Azure.
