---
title: Azure Standard Load Balancer- en beschikbaarheidszones
titleSuffix: Azure Load Balancer
description: Ga met dit leerpad aan de slag met Azure Standard Load Balancer en Availability Zones.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 5a65982c5c13eb4e4273efcfd8d14910b0f35572
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197144"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer van het type Standard en beschikbaarheidszones

Azure Standard Load Balancer ondersteunt [scenario's voor beschikbaarheidszones.](../availability-zones/az-overview.md) U Standard Load Balancer gebruiken om de beschikbaarheid in uw end-to-end scenario te optimaliseren door resources af te stemmen op zones en deze over zones te verdelen.  Bekijk [beschikbaarheidszones](../availability-zones/az-overview.md) voor richtlijnen over wat beschikbaarheidszones zijn, welke regio's momenteel beschikbaarheidszones ondersteunen en andere gerelateerde concepten en producten. Beschikbaarheidszones in combinatie met Standard Load Balancer is een uitgebreide en flexibele functieset die veel verschillende scenario's kan maken.  Bekijk dit document om inzicht te krijgen in deze [concepten](#concepts) en fundamentele richtlijnen voor [scenarioontwerp](#design).

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a>Concepten beschikbaarheidszones toegepast op Load Balancer

Een Load Balancer resource zelf is regionaal en nooit zonal. De granulariteit van wat u configureren, wordt beperkt door elke configuratie van frontend-, regel- en backendpooldefinitie.
In de context van beschikbaarheidszones worden het gedrag en de eigenschappen van een regel Load Balancer beschreven als zoneredundant of zonal.  Zone-redundant en zonale beschrijven de zonaliteit van een woning.  In de context van Load Balancer betekent zone-redundant altijd *meerdere zones* en zonale middelen om de service te isoleren tot *één zone*.
Zowel openbare als interne Load Balancer-ondersteuningszone- en zonale scenario's en beide kunnen het verkeer naar behoefte over zones leiden *(cross-zone load-balancing).* 

### <a name="frontend"></a>Front-end

Een Load Balancer frontend is een frontend IP-configuratie die verwijst naar een openbare IP-adresbron of een privé-IP-adres binnen het subnet van een virtuele netwerkbron.  Het vormt het laadpunt voor belasting waar uw service wordt blootgesteld.
Een load balancer-resource kan regels bevatten met zonale en zoneredundante frontends tegelijk. Wanneer een openbare IP-bron of een privé-IP-adres is gegarandeerd voor een zone, is de zonealiteit (of het ontbreken daarvan) niet veranderlijk.  Als u de zonealiteit van een openbare IP- of privé-IP-adresfrontend wilt wijzigen of weglaten, moet u het openbare IP opnieuw maken in de juiste zone.  Beschikbaarheidszones wijzigen de beperkingen voor meerdere frontend niet, bekijk [meerdere frontends voor Load Balancer](load-balancer-multivip-overview.md) voor meer informatie over deze mogelijkheid.

#### <a name="zone-redundant"></a>Zone overbodig 

In een regio met beschikbaarheidszones kan een Standard Load Balancer frontend zone-redundant zijn.  Zoneredundant betekent dat alle binnenkomende of uitgaande stromen worden bediend door meerdere beschikbaarheidszones in een regio die tegelijkertijd één IP-adres gebruiken. DNS-redundantieregelingen zijn niet vereist. Een enkel frontend IP-adres kan zonefalen overleven en kan worden gebruikt om alle (niet-beïnvloede) backendpoolleden te bereiken, ongeacht de zone. Een of meer beschikbaarheidszones kunnen mislukken en het gegevenspad overleeft zolang één zone in de regio gezond blijft. Het enkele IP-adres van de frontend wordt gelijktijdig bediend door meerdere onafhankelijke infrastructuurimplementaties in meerdere beschikbaarheidszones.  Dit betekent niet hitless data pad, maar elke retries of reestablishment zal slagen in andere zones niet beïnvloed door de zone mislukking.   

#### <a name="optional-zone-isolation"></a>Optionele zoneisolatie

U ervoor kiezen om een frontend gegarandeerd aan een enkele zone, die bekend staat als een *zonale frontend.*  Dit betekent dat elke inkomende of uitgaande stroom wordt bediend door één zone in een regio.  Uw frontend deelt het lot met de gezondheid van de zone.  Het gegevenspad wordt niet beïnvloed door fouten in andere zones dan waar het is gegarandeerd. U zonale frontends gebruiken om een IP-adres per beschikbaarheidszone bloot te leggen.  

Bovendien u zonale frontends direct gebruiken voor load balanced endpoints binnen elke zone. U dit ook gebruiken om per zone load-balanced endpoints bloot te leggen om elke zone individueel te controleren.  Of voor openbare eindpunten u ze integreren met een DNS load-balancing product zoals [Traffic Manager](../traffic-manager/traffic-manager-overview.md) en gebruik maken van een enkele DNS-naam. De client zal dan deze DNS-naam naar meerdere zonale IP-adressen oplossen.  

Als u deze concepten wilt combineren (zoneredundant en zonalvoor voor dezelfde backend), controleert u [meerdere frontends voor Azure Load Balancer](load-balancer-multivip-overview.md).

Voor een openbare frontend load balancer voegt u een *parameter voor zones* toe aan de openbare IP-bron waarnaar wordt verwezen door de ip-configuratie aan de frontend die door de desbetreffende regel wordt gebruikt.

Voeg voor een interne frontend load balancer een *parameter zones* toe aan de interne IP-configuratie load balancer. De zonale frontend zorgt ervoor dat de Load Balancer een IP-adres in een subnet garandeert voor een specifieke zone.

### <a name="cross-zone-load-balancing"></a>Cross-zone Load-Balancing

Cross-zone load-balancing is het vermogen van Load Balancer om een backend endpoint in elke zone te bereiken en is onafhankelijk van frontend en zijn zonaliteit.  Elke regel voor taakverdeling kan backend-instantie targeten in elke beschikbaarheidszone of regionale instanties.

U moet ervoor zorgen om uw scenario te construeren op een manier die een beschikbaarheid zones begrip uitgedrukt. U moet bijvoorbeeld uw virtuele machine-implementatie binnen één zone of meerdere zones garanderen en zonale frontend- en zonale backendresources op dezelfde zone afstemmen.  Als u beschikbaarheidszones kruist met alleen zonale bronnen, werkt het scenario, maar heeft het mogelijk geen duidelijke foutmodus met betrekking tot beschikbaarheidszones. 

### <a name="backend"></a>Back-end

Load Balancer werkt met virtuele machines exemplaren.  Dit kunnen zelfstandige, beschikbaarheidssets of virtuele machineschaalsets zijn.  Elke virtuele machine-instantie in één virtueel netwerk kan deel uitmaken van de backendpool, ongeacht of deze al dan niet is gegarandeerd voor een zone of aan welke zone was gegarandeerd.

Als u uw frontend en backend met één zone wilt uitlijnen en garanderen, plaatst u alleen virtuele machines binnen dezelfde zone in de desbetreffende backendpool.

Als u virtuele machines in meerdere zones wilt aanspreken, plaatst u virtuele machines uit meerdere zones in dezelfde backendpool.  Wanneer u virtuele machineschaalsets gebruikt, u een of meer virtuele machineschaalsets in dezelfde backendpool plaatsen.  En elk van deze virtuele machine schaal sets kunnen worden in een enkele of meerdere zones.

### <a name="outbound-connections"></a>Uitgaande verbindingen

Dezelfde zoneredundante en zonale eigenschappen zijn van toepassing op [uitgaande verbindingen](load-balancer-outbound-connections.md).  Een zoneredundant openbaar IP-adres dat wordt gebruikt voor uitgaande verbindingen, wordt door alle zones bediend. Een zoal openbaar IP-adres wordt alleen bediend door de zone waarin het is gegarandeerd.  Uitgaande snat-poorttoewijzingen overleven zonefouten en uw scenario blijft uitgaande SNAT-connectiviteit bieden als deze niet wordt beïnvloed door een zonefout.  Dit kan vereisen dat transmissies of verbindingen opnieuw worden ingesteld voor zoneredundante scenario's als een stroom werd bediend door een getroffen zone.  Stromen in andere zones dan de getroffen zones worden niet beïnvloed.

Het SNAT-poortpreallocation-algoritme is hetzelfde met of zonder beschikbaarheidszones.

### <a name="health-probes"></a>Statuscontroles

Uw bestaande definities van statussondes blijven zoals ze zijn zonder beschikbaarheidszones.  We hebben het gezondheidsmodel echter uitgebreid op infrastructuurniveau. 

Bij het gebruik van zoneredundante frontends breidt Load Balancer zijn interne statusmodel uit om onafhankelijk de bereikbaarheid van een virtuele machine vanuit elke beschikbaarheidszone te onderzoeken en paden af te sluiten in zones die mogelijk zijn mislukt zonder tussenkomst van de klant.  Als een bepaald pad niet beschikbaar is van de Load Balancer-infrastructuur van de ene zone naar een virtuele machine in een andere zone, kan Load Balancer deze fout detecteren en voorkomen. Andere zones die deze VM kunnen bereiken, kunnen de VM blijven bedienen vanaf hun respectievelijke frontends.  Als gevolg hiervan is het mogelijk dat elke zone tijdens foutgebeurtenissen iets verschillende verdelingen van nieuwe stromen heeft, terwijl de algehele status van uw end-to-end service wordt beschermd.

## <a name="design-considerations"></a><a name="design"></a>Ontwerpoverwegingen

Load Balancer is doelgericht flexibel in de context van beschikbaarheidszones. U ervoor kiezen om uit te lijnen op zones of u ervoor kiezen om zone-redundant te zijn voor elke regel.  Verhoogde beschikbaarheid kan komen op de prijs van verhoogde complexiteit en u moet ontwerpen voor beschikbaarheid voor optimale prestaties.  Laten we eens kijken naar een aantal belangrijke overwegingen van het ontwerp.

### <a name="automatic-zone-redundancy"></a>Automatische zoneredundantie

Load Balancer maakt het eenvoudig om een enkele IP te hebben als een zone-redundante frontend. Een zone-redundant IP-adres kan veilig dienen een zonale bron in elke zone en kan overleven een of meer zone mislukkingen, zolang een zone gezond blijft binnen de regio. Omgekeerd, een zonale frontend is een vermindering van de dienst tot een enkele zone en deelt het lot met de respectieve zone.

Zoneredundantie impliceert geen hitless datapath of controlevlak;  het is uitdrukkelijk gegevensvlak. Zone-redundante stromen kunnen gebruik maken van alle zones en de stromen van een klant zullen alle gezonde zones in een regio gebruiken. In het geval van zonestoring worden verkeersstromen die op dat moment gezonde zones gebruiken, niet beïnvloed.  Verkeersstromen die een zone gebruiken op het moment van zonestoring kunnen worden beïnvloed, maar toepassingen kunnen herstellen. Deze stromen kunnen worden voortgezet in de resterende gezonde zones binnen de regio bij het opnieuw worden verzonden of opnieuw worden ingesteld, zodra Azure is geconvergeerd rond de zonefout.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a>Grenzen van de zone

Het is belangrijk om te begrijpen dat elke keer dat een end-to-end service zones doorkruist, je het lot deelt met niet één zone, maar mogelijk meerdere zones.  Als gevolg hiervan is het mogelijk dat uw end-to-end service geen beschikbaarheid heeft verkregen ten opzichte van niet-zonale implementaties.

Vermijd de invoering van onbedoelde afhankelijkheden tussen verschillende zones, waardoor de beschikbaarheidswinst bij het gebruik van beschikbaarheidszones teniet wordt gedaan.  Wanneer uw toepassing uit meerdere componenten bestaat en u bestand wilt zijn tegen zonefalen, moet u ervoor zorgen dat voldoende kritieke componenten overleven in het geval van een zonefalen.  Een enkele kritieke component voor uw toepassing kan bijvoorbeeld van invloed zijn op uw hele toepassing als deze alleen bestaat in een andere zone dan de overlevende zone(s).  Daarnaast ook rekening houden met de zone restauratie en hoe uw toepassing zal convergeren. U moet begrijpen hoe uw toepassing redenen met betrekking tot mislukkingen van delen van het. Laten we een aantal belangrijke punten bekijken en ze gebruiken als inspiratie voor vragen terwijl u door uw specifieke scenario denkt.

- Als uw toepassing twee componenten heeft, zoals een IP-adres en een virtuele machine met beheerde schijf, en deze zijn gegarandeerd in zone 1 en zone 2, wanneer zone 1 mislukt, zal uw end-to-end-service niet overleven wanneer zone 1 uitvalt.  Kruis zones niet met zonale scenario's, tenzij u volledig begrijpt dat u een potentieel gevaarlijke foutmodus maakt.  Dit scenario is toegestaan om flexibiliteit te bieden.

- Als uw toepassing twee componenten heeft, zoals een IP-adres en een virtuele machine met beheerde schijf, en deze zijn gegarandeerd respectievelijk zoneredundant en zone 1, overleeft uw end-to-end-service zonefalen van zone 2, zone 3 of beide, tenzij zone 1 is mislukt.  Echter, je verliest een aantal mogelijkheid om reden over de gezondheid van uw dienst als alles wat je observeert is de bereikbaarheid van de frontend.  Overweeg het ontwikkelen van een uitgebreider gezondheids- en capaciteitsmodel.  U zoneredundante en zonale concepten samen gebruiken om inzicht en beheersbaarheid uit te breiden.

- Als uw toepassing twee componenten heeft, zoals een zoneredundante Load Balancer frontend en een cross-zone virtuele machineschaal in drie zones, zijn uw resources in zones die niet door een storing zijn getroffen, beschikbaar, maar kan uw end-to-end servicecapaciteit worden afgebroken tijdens zonestoring. Vanuit het oogpunt van de infrastructuur kan uw implementatie een of meer zonefouten overleven en dit roept de volgende vragen op:
  - Begrijpt u hoe uw toepassing redenen over dergelijke storingen en verminderde capaciteit?
  - Heeft u waarborgen nodig in uw dienst om een failover te forceren naar een regiopaar indien nodig?
  - Hoe gaat u een dergelijk scenario monitoren, detecteren en beperken? Mogelijk u de diagnose Van Standard Load Balancer gebruiken om de controle op uw end-to-end serviceprestaties uit te breiden. Bedenk wat er beschikbaar is en wat kan augmentatie nodig hebben voor een compleet beeld.

- Zones kunnen fouten gemakkelijker te begrijpen en te bevatten.  Zonefout is echter niet anders dan andere fouten als het gaat om concepten zoals time-outs, nieuwe pogingen en back-offalgoritmen. Hoewel Azure Load Balancer zoneredundante paden biedt en probeert snel te herstellen, kunnen op pakketniveau in realtime retransmissions of re-instellingen optreden tijdens het begin van een storing en is het belangrijk om te begrijpen hoe uw toepassing omgaat met Mislukkingen. Uw load-balancing schema zal overleven, maar je moet plannen voor het volgende:
  - Wanneer een zone uitvalt, begrijpt uw end-to-end service dit en als de status verloren gaat, hoe herstelt u dan?
  - Wanneer een zone terugkeert, begrijpt uw toepassing dan hoe veilig te convergeren?

Bekijk [azure-cloudontwerppatronen](https://docs.microsoft.com/azure/architecture/patterns/) om de tolerantie van uw toepassing voor foutscenario's te verbeteren.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [beschikbaarheidszones](../availability-zones/az-overview.md)
- Meer informatie over [Standard Load Balancer](load-balancer-standard-overview.md)
- Meer informatie over het [laden van balance VM's binnen een zone met behulp van een Standaard Load Balancer met een zonale frontend](load-balancer-standard-public-zonal-cli.md)
- Meer informatie over het [laden van balansVM's in zones met behulp van een Standaard Load Balancer met een zoneredundante frontend](load-balancer-standard-public-zone-redundant-cli.md)
- Meer informatie over [Azure-cloudontwerppatronen](https://docs.microsoft.com/azure/architecture/patterns/) om de tolerantie van uw toepassing voor foutscenario's te verbeteren.
