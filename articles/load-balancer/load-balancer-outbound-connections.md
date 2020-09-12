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
ms.date: 06/24/2020
ms.author: allensu
ms.openlocfilehash: 4368a025ecc158afa1ee78b8abd86bd6db42ba75
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438662"
---
# <a name="outbound-connections-in-azure"></a>Uitgaande verbindingen in Azure

Azure Load Balancer biedt uitgaande connectiviteit via verschillende mechanismen. In dit artikel worden de scenario's beschreven en hoe u deze beheert. Als u problemen ondervindt met uitgaande verbindingen via een Azure Load Balancer, raadpleegt u de [hand leiding voor het oplossen van problemen met uitgaande verbindingen](../load-balancer/troubleshoot-outbound-connection.md).

>[!NOTE]
>In dit artikel worden de implementaties van Resource Manager besproken. Micro soft raadt Resource Manager aan voor productie werkbelastingen.

## <a name="terminology"></a>Terminologie

| Termijn | Toepasselijke Protocol (s) | Details|
| ---------|---------| -------|
| Bron Network Address Translation (SNAT) | TCP, UDP | Een implementatie in azure kan communiceren met eind punten buiten Azure in de open bare IP-adres ruimte. Wanneer een exemplaar een uitgaande stroom initieert naar een bestemming in de open bare IP-adres ruimte, wijst Azure het privé-IP-adres dynamisch toe aan een openbaar IP-adres. Nadat deze toewijzing is gemaakt, kan het retour verkeer voor deze uitgaande stroom van het doel ook het privé-IP-adres bereiken waarvan de stroom afkomstig is. Azure gebruikt **bron Network Address Translation (SNAT)** om deze functie uit te voeren.|
| Poort maskering SNAT (PAT)| TCP, UDP |  Wanneer meerdere privé-IP-adressen worden gemaskeerd achter één openbaar IP-adres, gebruikt Azure **poort adres omzetting (Pat)** voor het maskeren/verbergen van privé-IP-adressen. Tijdelijke poorten worden gebruikt voor PAT en zijn [vooraf toegewezen](#preallocatedports) op basis van pool grootte. Wanneer een open bare Load Balancer resource is gekoppeld aan VM-exemplaren, die geen toegewezen open bare IP-adressen hebben, wordt elke uitgaande verbindings bron herschreven. De bron wordt herschreven van de privé-IP-adres ruimte van het virtuele netwerk naar het open bare IP-adres van de load balancer. In de open bare IP-adres ruimte moet de 5-tuple van de stroom (bron-IP-adres, bron poort, IP-transport protocol, doel-IP-adres, doel poort) uniek zijn. Het SNAT-poort masker kan worden gebruikt met TCP-of UDP IP-protocollen. Tijdelijke poorten (SNAT-poorten) worden gebruikt om dit te verhelpen na het herschrijven van het IP-adres van de privé bron, omdat meerdere stromen afkomstig zijn van één openbaar IP-adres. Met het SNAT-algoritme voor poort maskering worden de SNAT-poorten anders toegewezen voor UDP versus TCP.|
| SNAT-poorten| TCP | SNAT-poorten zijn tijdelijke poorten die beschikbaar zijn voor een bepaald openbaar IP-bron adres. Eén SNAT-poort wordt per stroom verbruikt naar één doel-IP-adres, poort. Voor meerdere TCP-stromen naar hetzelfde doel-IP-adres, dezelfde poort en hetzelfde protocol gebruikt elke TCP-stroom één SNAT-poort. Dit zorgt ervoor dat de stromen uniek zijn wanneer ze afkomstig zijn uit hetzelfde open bare IP-adres en naar hetzelfde doel-IP-adres, dezelfde poort en hetzelfde protocol gaan. Meerdere stromen, elk naar een ander doel-IP-adres, poort en protocol, delen één SNAT-poort. Het doel-IP-adres, de poort en het Protocol maken stroom uniek zonder dat er extra bron poorten nodig zijn om stromen te onderscheiden van de open bare IP-adres ruimte.|
|SNAT-poorten | UDP | UDP SNAT-poorten worden beheerd door een ander algoritme dan TCP-SNAT-poorten.  Load Balancer gebruikt een algoritme dat wordt aangeduid als ' Port-restricted kegel NAT ' voor UDP.  Er wordt één SNAT-poort verbruikt voor elke stroom, ongeacht het doel-IP-adres, de poort.|
| Uitputting | - | Wanneer de SNAT-poort resources zijn uitgeput, mislukken uitgaande stromen totdat bestaande stromen van SNAT-poorten worden vrijgegeven. Load Balancer overschrijden de SNAT-poorten wanneer de stroom wordt gesloten en gebruikt een [time-out van 4 minuten](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) voor het vrijmaken van de SNAT-poorten van niet-actieve stromen. UDP SNAT-poorten lopen doorgaans veel sneller dan TCP SNAT-poorten vanwege het verschil in gebruikte algoritmen. U moet de test met dit verschil ontwerpen en schalen.|
| Release van SNAT-poort | TCP | Als de server/client FINACK verzendt, wordt de SNAT-poort na 240 seconden vrijgegeven. Als een RST wordt gezien, wordt de SNAT-poort na 15 seconden vrijgegeven. Als er een time-out voor inactiviteit is bereikt, wordt de poort vrijgegeven.|
| Release van SNAT-poort | UDP |Als er een time-out voor inactiviteit is bereikt, wordt de poort vrijgegeven.|
| SNAT-poort opnieuw gebruiken | TCP, UDP | Zodra een poort is vrijgegeven, kan de poort zo nodig opnieuw worden gebruikt.  U kunt SNAT-poorten beschouwen als een reeks van laag naar Maxi maal beschikbaar voor een bepaald scenario en de eerste beschik bare SNAT-poort wordt gebruikt voor nieuwe verbindingen.|

### <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>Poort toewijzings algoritme

Azure gebruikt een algoritme om te bepalen hoeveel vooraf toegewezen SNAT-poorten beschikbaar zijn op basis van de grootte van de back-end-groep wanneer u PAT gebruikt. Voor elk open bare IP-adres dat is gekoppeld aan een load balancer zijn er 64.000 poorten beschikbaar als SNAT-poorten voor elk IP-transport protocol. Hetzelfde aantal SNAT-poorten wordt vooraf toegewezen voor UDP en TCP, respectievelijk onafhankelijk per IP-transport protocol.  Het SNAT-poort gebruik is echter anders, afhankelijk van het feit of de stroom UDP of TCP is. Wanneer uitgaande stromen worden gemaakt, worden deze poorten dynamisch verbruikt (Maxi maal de vooraf toegewezen limiet) en vrijgegeven wanneer de stroom wordt gesloten of [time-outs voor inactiviteit](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) optreden. Poorten worden alleen gebruikt als het nodig is om stromen uniek te maken.

#### <a name="dynamic-snat-ports-allocated"></a><a name="snatporttable"></a> Toegewezen dynamische SNAT-poorten

In de volgende tabel ziet u de SNAT-poort voortoewijzingen voor lagen van back-endadresgroep-Pools:

| Pool grootte (VM-exemplaren) | Vooraf toegewezen SNAT-poorten per IP-configuratie |
| --- | --- |
| 1-50 | 1.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

Het wijzigen van de grootte van uw back-end-pool kan invloed hebben op sommige van uw vastgelegde stromen:

- Als de grootte van de back-end-groep toeneemt en overschakelt naar de volgende laag, worden de helft van uw vooraf toegewezen SNAT-poorten vrijgemaakt tijdens de overgang naar de volgende grotere back-endadresgroep. Stromen die zijn gekoppeld aan een vrijgestelde SNAT-poort, worden getimed en moeten opnieuw worden ingesteld. Als er een nieuwe stroom wordt gedaan, wordt de stroom onmiddellijk uitgevoerd zolang er vooraf toegewezen poorten beschikbaar zijn.
- Als de back-end-pool grootte vermindert en overgangen naar een lagere laag, neemt het aantal beschik bare SNAT-poorten toe. In dit geval worden bestaande toegewezen SNAT-poorten en de bijbehorende stromen niet beïnvloed.

## <a name="outbound-connections-scenario-overview"></a><a name="scenarios"></a>Overzicht van scenario voor uitgaande verbindingen

| Scenario | Methode | IP-protocollen | Beschrijving |
|  --- | --- | --- | --- |
|  1. VM met een openbaar IP-adres (met of zonder Azure Load Balancer | SNAT, poort masker niet gebruikt | TCP, UDP, ICMP, ESP | Azure gebruikt het open bare IP-adres dat is toegewezen aan de IP-configuratie van de NIC van het exemplaar voor alle uitgaande stromen. Alle tijdelijke poorten zijn beschikbaar voor het exemplaar. Het maakt niet uit of de virtuele machine gelijkmatig is verdeeld of niet. Dit scenario heeft voor rang op de andere. Een openbaar IP-adres dat is toegewezen aan een virtuele machine is een 1:1-relatie (in plaats van 1: veel) en geïmplementeerd als een stateless 1:1 NAT. |
| 2. open bare Load Balancer gekoppeld aan een virtuele machine (geen openbaar IP-adres op de VM/exemplaar) | SNAT met poort maskering (PAT) met behulp van de Load Balancer-frontends | TCP, UDP | In dit scenario moet de Load Balancer bron worden geconfigureerd met een load balancer regel om een koppeling te maken tussen de open bare IP-front-end met de back-end-groep. Als u deze regel configuratie niet voltooit, wordt het gedrag in scenario 3 beschreven. Het is niet nodig dat de regel een werkende listener in de back-end-pool heeft om de status test te laten slagen. Wanneer een virtuele machine een uitgaande stroom maakt, vertaalt Azure het IP-adres van de privé bron van de uitgaande stroom naar het open bare IP-adres van de open bare Load Balancer frontend via SNAT. Tijdelijke poorten van het front-end-open bare IP-adres van de load balancer worden gebruikt voor het onderscheiden van afzonderlijke stromen die afkomstig zijn van de virtuele machine. SNAT maakt dynamisch gebruik van [vooraf toegewezen tijdelijke poorten](#preallocatedports) wanneer uitgaande stromen worden gemaakt. In deze context worden de tijdelijke poorten die worden gebruikt voor SNAT de SNAT-poorten genoemd. SNAT-poorten worden vooraf toegewezen, zoals wordt beschreven in de [standaard tabel SNAT-poorten toegewezen](#snatporttable). |
| 3. VM (geen Load Balancer, geen openbaar IP-adres) of VM gekoppeld aan basis interne Load Balancer | SNAT met poort maskering (PAT) | TCP, UDP | Wanneer de virtuele machine een uitgaande stroom maakt, vertaalt Azure het IP-adres van de privé bron van de uitgaande stroom naar een IP-adres van een open bare bron. Dit open bare IP-adres kan **niet worden geconfigureerd**, kan niet worden gereserveerd en telt niet op basis van de open bare IP-resource limiet van het abonnement. Als u de virtuele machine of Beschikbaarheidsset of de VM-schaalset opnieuw implementeert, wordt dit open bare IP-adres vrijgegeven en wordt er een nieuw openbaar IP-adres aangevraagd. Gebruik dit scenario niet voor White List IP-adressen. Gebruik in plaats daarvan scenario 1 of 2 waar u het uitgaande gedrag expliciet declareert. SNAT-poorten zijn vooraf toegewezen, zoals wordt beschreven in de [standaard tabel met SNAT-poorten](#snatporttable).

## <a name="outbound-rules"></a><a name="outboundrules"></a>Uitgaande regels

 Met uitgaande regels kunt u de uitgaande Network Address Translation van open bare [Standard Load Balancer](load-balancer-standard-overview.md)eenvoudig configureren.  U hebt volledige declaratieve controle over uitgaande connectiviteit om deze mogelijkheid te schalen en af te stemmen op uw specifieke behoeften. In deze sectie wordt scenario 2 (B) in hierboven beschreven.

![Load Balancer uitgaande regels](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Met uitgaande regels kunt u Load Balancer de uitgaande NAT helemaal opnieuw te definiëren. U kunt ook het gedrag van bestaande uitgaande NAT schalen en afstemmen.

Met uitgaande regels kunt u het volgende beheren:

- welke virtuele machines moeten worden vertaald naar de open bare IP-adressen.
- hoe uitgaande SNAT-poorten moeten worden toegewezen.
- de protocollen waarvoor een uitgaande vertaling moet worden geboden.
- welke duur moet worden gebruikt voor de time-out voor uitgaande verbindingen (4-120 minuten).
- of een TCP-Reset moet worden verzonden bij een time-out voor inactiviteit
- TCP-en UDP-transport protocollen met één regel

### <a name="outbound-rule-definition"></a>Definitie van uitgaande regel

Net als alle Load Balancer regels volgen uitgaande regels dezelfde vertrouwde syntaxis als taak verdeling en binnenkomende NAT-regels: **frontend**-  +  **para meters**  +  **back-endadresgroep**. Met een uitgaande regel wordt uitgaande NAT geconfigureerd voor _alle virtuele machines die door de back-endadresgroep worden geïdentificeerd_ om te worden vertaald naar de front- _End_.  De _para meters_ bieden extra nauw keurige controle over de uitgaande NAT-algoritme.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Uitgaand NAT schalen met meerdere IP-adressen

Elk extra IP-adres dat wordt geleverd door een front-end biedt extra 64.000 tijdelijke poorten voor Load Balancer die als SNAT-poorten kunnen worden gebruikt. U kunt meerdere IP-adressen gebruiken om te plannen voor grootschalige scenario's en u kunt uitgaande regels gebruiken om te voor komen dat er gevoelige patronen voor de [uitputting van SNAT](troubleshoot-outbound-connection.md#snatexhaust) zijn.  

U kunt ook een [openbaar IP-voor voegsel](https://aka.ms/lbpublicipprefix) rechtstreeks met een uitgaande regel gebruiken.  Met behulp van het voor voegsel openbaar IP-adres kunt u eenvoudig schalen en vereenvoudigd wit-overzicht van stromen die afkomstig zijn van uw Azure-implementatie. U kunt een front-end-IP-configuratie binnen de Load Balancer resource configureren om rechtstreeks naar een open bare IP-adres voorvoegsel te verwijzen.  Hierdoor is Load Balancer exclusieve controle over het open bare IP-voor voegsel en worden alle open bare IP-adressen die zich in het open bare IP-voor voegsel voor uitgaande verbindingen bevinden, automatisch door de uitgaande regel gebruikt.  Elk van de IP-adressen binnen het open bare IP-voor voegsel bieden een extra 64.000 tijdelijke poorten per IP-adres voor Load Balancer om te gebruiken als SNAT-poorten.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Uitgaande time-out voor niet-actieve stroom en TCP Reset

Uitgaande regels bieden een configuratie parameter voor het beheren van de uitgaande time-out voor niet-actieve stroom en om deze te vergelijken met de behoeften van uw toepassing. Uitgaande time-outs voor inactiviteit zijn standaard ingesteld op 4 minuten. U kunt meer informatie over het [configureren van time-outs voor inactiviteit](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout). Het standaard gedrag van Load Balancer is om de stroom op de achtergrond neer te zetten wanneer de uitgaande time-out voor inactiviteit is bereikt.  Met de `enableTCPReset` para meter kunt u een meer voorspel bare toepassings gedrag inschakelen en bepalen of u twee richtingen TCP Reset (TCP RST) wilt verzenden wanneer de uitgaande time-out voor inactiviteit is verlopen. [TCP Reset controleren bij time-out voor inactiviteit](https://aka.ms/lbtcpreset) voor meer informatie over de beschik baarheid van regio's.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Uitgaande connectiviteit voor komen

Taakverdelings regels bieden automatische programmering van uitgaande NAT. Sommige scenario's profiteren echter of vereisen dat u de automatische programmering van uitgaande NAT door de taakverdelings regel uitschakelt, zodat u het gedrag kunt beheren of verfijnen.  
U kunt deze para meter op twee manieren gebruiken:

1. Optionele onderdrukking van het gebruik van het inkomende IP-adres voor uitgaande SNAT via uitgaande SNAT uitschakelen voor een taakverdelings regel
  
2. Stem de uitgaande SNAT-para meters af van een IP-adres dat tegelijkertijd wordt gebruikt voor inkomend en uitgaand.  De automatische uitgaand NAT-programmering moet worden uitgeschakeld zodat een regel voor uitgaand verkeer kan worden beheerd.  Als u bijvoorbeeld de toewijzing van de SNAT-poort wilt wijzigen van een adres dat ook voor binnenkomend verkeer wordt gebruikt, `disableOutboundSnat` moet u de para meter instellen op True.  Als u probeert een regel voor uitgaande verbindingen te gebruiken om de para meters van een IP-adres dat ook wordt gebruikt voor inkomend verkeer opnieuw te definiëren en geen uitgaande NAT-programmering van de taakverdelings regel heeft vrijgegeven, mislukt de bewerking voor het configureren van een uitgaande regel.

>[!IMPORTANT]
> De virtuele machine heeft geen uitgaande verbinding als u deze para meter instelt op True en geen uitgaande regel hebt om uitgaande connectiviteit te definiëren.  Sommige bewerkingen van uw virtuele machine of uw toepassing kunnen afhankelijk zijn van een uitgaande verbinding. Zorg ervoor dat u bekend bent met de afhankelijkheden van uw scenario en dat dit gevolgen heeft voor het maken van deze wijziging.

Soms is het niet wenselijk dat een virtuele machine een uitgaande stroom maakt. Of er is mogelijk een vereiste om te beheren welke doelen kunnen worden bereikt met uitgaande stromen of welke doelen binnenkomende stromen kunnen starten. In dit geval kunt u [netwerk beveiligings groepen](../virtual-network/security-overview.md) gebruiken om de doelen te beheren die de virtuele machine kan bereiken. U kunt Nsg's ook gebruiken om te beheren welke open bare bestemming binnenkomende stromen kan initiëren.

Wanneer u een NSG toepast op een virtuele machine met taak verdeling, moet u rekening houden met de [service Tags](../virtual-network/security-overview.md#service-tags) en de [standaard beveiligings regels](../virtual-network/security-overview.md#default-security-rules). U moet ervoor zorgen dat de VM Health probe-aanvragen van Azure Load Balancer kan ontvangen.

Als een NSG de status test aanvragen blokkeert vanuit het standaard label AZURE_LOADBALANCER, mislukt de VM-status test en wordt de virtuele machine gemarkeerd. Load Balancer stopt met het verzenden van nieuwe stromen naar die virtuele machine.

## <a name="scenarios-with-outbound-rules"></a>Scenario's met uitgaande regels

| # | Scenario| Details|
|---|---|---|
| I | Uitgaande verbindingen met een specifieke set open bare IP-adressen opschonen| U kunt een uitgaande regel voor het opschonen van uitgaande verbindingen gebruiken om aan te geven dat ze afkomstig zijn uit een specifieke set open bare IP-adressen om white list-scenario's te vereenvoudigen.  Het open bare IP-adres van de bron kan hetzelfde zijn als het gebruik van een taakverdelings regel of een andere set open bare IP-adressen dan wordt gebruikt door een taakverdelings regel.  1. Maak een [openbaar IP-voor voegsel](https://aka.ms/lbpublicipprefix) (of open bare IP-adressen van open bare IP-voor voegsel) 2. Maak een open bare Standard Load Balancer 3. Een front-end maken die verwijst naar het open bare IP-voor voegsel (of open bare IP-adressen) die u wilt gebruiken 4. Hergebruik een back-end-pool of maak een back-end-pool en plaats de virtuele machines in een back-end-groep van de open bare Load Balancer 5. Configureer een uitgaande regel voor de open bare Load Balancer om uitgaande NAT voor deze Vm's te Program meren met de front-end. Als u niet wilt dat de taakverdelings regel voor uitgaand verkeer wordt gebruikt, moet u uitgaand SNAT uitschakelen op de taakverdelings regel.
| OORLOG | Toewijzing van SNAT-poort wijzigen| U kunt uitgaande regels gebruiken voor het afstemmen van de [automatische toewijzing van de SNAT-poort op basis van de back-endadresgroep](load-balancer-outbound-connections.md#preallocatedports). Als u bijvoorbeeld twee virtuele machines hebt die één openbaar IP-adres voor uitgaande NAT delen, kunt u het aantal toegewezen SNAT-poorten van de standaard 1024-poorten verhogen als u de SNAT-uitputting ondervindt. Elk openbaar IP-adres kan bijdragen aan Maxi maal 64.000 tijdelijke poorten.  Als u een uitgaande regel configureert met één openbaar IP-adres front-end, kunt u een totaal van 64.000 SNAT-poorten distribueren naar Vm's in de back-end-pool.  Voor twee virtuele machines kunnen Maxi maal 32.000 SNAT-poorten worden toegewezen met een uitgaande regel (2x 32.000 = 64.000). U kunt regels voor uitgaande verbindingen gebruiken om de toegewezen SNAT-poorten standaard af te stemmen. U kunt meer of minder toewijzen dan de standaard toewijzing van de SNAT-poort. Elk openbaar IP-adres van alle frontends van een uitgaande regel draagt bij aan Maxi maal 64.000 tijdelijke poorten voor gebruik als SNAT-poorten.  Load Balancer wijst SNAT-poorten toe in veelvouden van 8. Als u een waarde opgeeft die niet deelbaar is door 8, wordt de configuratie bewerking geweigerd.  Als u meer SNAT-poorten probeert toe te wijzen dan beschikbaar zijn op basis van het aantal open bare IP-adressen, wordt de configuratie bewerking geweigerd.  Als u bijvoorbeeld 10.000 poorten per VM toewijst en 7 virtuele machines in een back-end-pool een enkel openbaar IP-adres delen, wordt de configuratie geweigerd (7 x 10.000 SNAT-poorten > 64.000 SNAT-poorten).  U kunt meer open bare IP-adressen toevoegen aan de front-end van de regel voor uitgaande verbindingen om het scenario in te scha kelen. U kunt teruggaan naar de [standaard toewijzing van de SNAT-poort op basis van de grootte](load-balancer-outbound-connections.md#preallocatedports) van de back-endadresgroep door 0 op te geven voor het aantal poorten. In dat geval krijgen de eerste 50 VM-exemplaren 1024 poorten, worden 51-100 VM-exemplaren 512 en dus weer op basis van de [tabel](#snatporttable).|
| DERDE| Alleen uitgaand verkeer inschakelen | U kunt een open bare Standard Load Balancer gebruiken om uitgaande NAT te bieden voor een groep Vm's. In dit scenario kunt u zelf een regel voor uitgaande verbindingen gebruiken, zonder dat u extra regels nodig hebt.|
| 4 | Alleen uitgaand NAT voor Vm's (geen inkomend) | Definieer een open bare Standard Load Balancer, plaats de virtuele machines in de back-end-pool en configureer een uitgaande regel voor het Program meren van uitgaande NAT en schoon de uitgaande verbindingen op die afkomstig zijn van een specifiek openbaar IP-adres. U kunt ook een openbaar IP-voor voegsel gebruiken dat is vereenvoudigd wit: de bron van uitgaande verbindingen weer geven. 1. Maak een open bare Standard Load Balancer. 2. Maak een back-end-pool en plaats de virtuele machines in een back-end-groep van de open bare Load Balancer. 3. Een uitgaande regel configureren op de open bare Load Balancer om uitgaande NAT voor deze Vm's te Program meren.
| V| Uitgaande NAT voor interne Standard Load Balancer scenario's| Wanneer u een interne Standard Load Balancer gebruikt, is uitgaand NAT niet beschikbaar totdat de uitgaande connectiviteit expliciet is gedeclareerd. Met de volgende stappen kunt u uitgaande connectiviteit definiëren met behulp van een uitgaande regel voor het maken van uitgaande connectiviteit voor Vm's achter een interne Standard Load Balancer: 1. Maak een open bare Standard Load Balancer. 2. Maak een back-end-pool en plaats de virtuele machines in een back-end-groep van de open bare Load Balancer naast de interne Load Balancer. 3. Een uitgaande regel configureren op de open bare Load Balancer om uitgaande NAT voor deze Vm's te Program meren. Raadpleeg [dit voor beeld](https://docs.microsoft.com/azure/load-balancer/egress-only)voor meer informatie over dit scenario. |
| VI | TCP & UDP-protocollen inschakelen voor uitgaande NAT met een openbaar Standard Load Balancer | Wanneer u een open bare Standard Load Balancer gebruikt, komt de automatische uitgaande NAT-programmering die overeenkomt met het transport protocol van de taakverdelings regel. 1. Schakel uitgaande SNAT uit op de taakverdelings regel. 2. Een uitgaande regel configureren op hetzelfde Load Balancer. 3. Gebruik de back-end-groep die al door uw virtuele machines wordt gebruikt. 4. Geef ' Protocol ': ' all ' op als onderdeel van de uitgaande regel. Wanneer alleen binnenkomende NAT-regels worden gebruikt, wordt er geen uitgaand NAT-verkeer gegeven. 1. Virtuele machines in een back-end-groep plaatsen. 2. Definieer een of meer frontend-IP-configuraties met een openbaar IP-adres (sen) of open bare IP-voor voegsel 3. Een uitgaande regel configureren op hetzelfde Load Balancer. 4. Geef ' Protocol ': ' all ' op als onderdeel van de uitgaande regel |


## <a name="limitations"></a>Beperkingen

- Het maximale aantal bruikbare tijdelijke poorten per frontend-IP-adres is 64.000.
- Het bereik van de Configureer bare uitgaande time-out voor inactiviteit is 4 tot 120 minuten (240 tot 7200 seconden).
- Load Balancer biedt geen ondersteuning voor ICMP voor uitgaande NAT.
- Uitgaande regels kunnen alleen worden toegepast op de primaire IP-configuratie van een NIC.  U kunt geen uitgaande regel maken voor het secundaire IP-adres van een virtuele machine of NVA. Er worden meerdere Nic's ondersteund.
- Rollen van webwerkers zonder VNet en andere micro soft-platform Services kunnen toegankelijk zijn wanneer alleen een interne Standard Load Balancer wordt gebruikt als gevolg van een neven effect van de manier waarop de voor bereide en andere functie van de platform services functioneren. Vertrouw niet op deze zijde als de respectieve service zelf of het onderliggende platform kan zonder kennisgeving worden gewijzigd. U moet altijd aannemen dat u een uitgaande connectiviteit expliciet moet maken als u alleen een interne Standard Load Balancer wilt gebruiken. Scenario 3 dat in dit artikel wordt beschreven, is niet beschikbaar.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Standard Load Balancer](load-balancer-standard-overview.md).
- Bekijk onze [Veelgestelde vragen over Azure Load Balancer](load-balancer-faqs.md).
- Meer informatie over [Uitgaande regels](load-balancer-outbound-rules-overview.md) voor standaard open bare Load Balancer.
- Meer informatie over [Load Balancer](load-balancer-overview.md).
- Meer informatie over [netwerk beveiligings groepen](../virtual-network/security-overview.md).
- Meer informatie over een aantal van de andere belang rijke [netwerk mogelijkheden](../networking/networking-overview.md) van Azure.
