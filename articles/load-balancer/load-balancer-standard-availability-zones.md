---
title: Azure Standard Load Balancer en Beschikbaarheidszones
titleSuffix: Azure Load Balancer
description: Met dit leer traject kunt u aan de slag met Azure Standard Load Balancer en Beschikbaarheidszones.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78197144"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer van het type Standard en beschikbaarheidszones

Azure Standard Load Balancer ondersteunt scenario's met [beschikbaarheids zones](../availability-zones/az-overview.md) . U kunt Standard Load Balancer gebruiken om de beschik baarheid in uw end-to-end-scenario te optimaliseren door resources te uitlijnen met zones en deze over te verdelen over zones.  Bekijk [beschikbaarheids zones](../availability-zones/az-overview.md) voor meer informatie over welke beschikbaarheids zones, welke regio's momenteel ondersteuning bieden voor beschikbaarheids zones en andere gerelateerde concepten en producten. Beschikbaarheids zones in combi natie met Standard Load Balancer is een expansieve,-en flexibele functieset die veel verschillende scenario's kan maken.  Lees dit document voor meer informatie over deze [concepten](#concepts) en [ontwerp richtlijnen](#design)voor het basis scenario.

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a>Beschikbaarheidszones concepten toegepast op Load Balancer

Een Load Balancer resource zelf is regionaal en nooit zonegebonden. De granulatie van wat u kunt configureren, wordt beperkt door elke configuratie van de definitie van de frontend, regel en back-endserver.
In de context van beschikbaarheids zones worden het gedrag en de eigenschappen van een Load Balancer regel beschreven als zone-redundante of zonegebonden.  Zone-redundante en zonegebonden beschrijven de zonality van een eigenschap.  In de context van Load Balancer betekent zone-redundantie altijd dat *meerdere zones* en zonegebonden betekent dat de service wordt geïsoleerd voor *één zone*.
Zowel de open bare als de interne Load Balancer ondersteunings zone-redundante en zonegebonden-scenario's en beide kunnen verkeer omleiden naar alle zones, indien nodig (*Kruis zone taak verdeling*). 

### <a name="frontend"></a>Front-end

Een Load Balancer frontend is een front-end-IP-configuratie die verwijst naar een open bare IP-adres of een privé-IP-adres binnen het subnet van een virtuele netwerk bron.  Het vormt het eind punt met gelijke taak verdeling waar uw service wordt weer gegeven.
Een Load Balancer resource kan regels bevatten met zonegebonden en een zone-redundante front-end. Wanneer een open bare IP-bron of een privé-IP-adres is gegarandeerd voor een zone, is het zonality (of het ontbreken daarvan) niet veranderbaar.  Als u de zonality van een openbaar IP-of privé-IP-adres wilt wijzigen of weglaten, moet u de open bare IP-adressen in de juiste zone opnieuw maken.  In beschikbaarheids zones worden de beperkingen voor meerdere frontends niet gewijzigd. Controleer meerdere front-ends [voor Load Balancer](load-balancer-multivip-overview.md) voor meer informatie over deze mogelijkheid.

#### <a name="zone-redundant"></a>Zone redundant 

Een Standard Load Balancer frontend kan in een regio met beschikbaarheids zones zone-redundant zijn.  Zone-redundant houdt in dat alle binnenkomende of uitgaande stromen worden geleverd door meerdere beschikbaarheids zones in een regio tegelijk met één IP-adres. DNS-redundantie schema's zijn niet vereist. Eén frontend-IP-adres kan onschadelijk worden gemaakt aan de zone en kan worden gebruikt voor het bereiken van alle (niet-getroffen) back-end-groeps leden, onafhankelijk van de zone. Een of meer beschikbaarheids zones kunnen mislukken en het gegevenspad blijft actief zolang er één zone in de regio in orde is. Het enkelvoudige IP-adres van de frontend wordt tegelijkertijd bediend door meerdere onafhankelijke infrastructuur implementaties in meerdere beschikbaarheids zones.  Dit betekent niet het Hitless, maar eventuele nieuwe pogingen of het opnieuw instellen van het bestand slaagt in andere zones die niet van invloed zijn op de storing van de zone.   

#### <a name="optional-zone-isolation"></a>Optionele zone isolatie

U kunt ervoor kiezen een front-end te garanderen voor één zone, die wordt aangeduid als een zonegebonden-front- *End*.  Dit betekent dat elke binnenkomende of uitgaande stroom wordt bediend door één zone in een regio.  Uw front-end-shares worden meegezonden met de status van de zone.  Het gegevenspad wordt niet beïnvloed door storingen in andere zones dan waar het is gegarandeerd. U kunt zonegebonden-frontends gebruiken om een IP-adres per beschikbaarheids zone beschikbaar te maken.  

Daarnaast kunt u zonegebonden-front-ends rechtstreeks gebruiken voor eind punten met gelijke taak verdeling in elke zone. U kunt dit ook gebruiken voor het weer geven van eind punten met gelijke taak verdeling voor elke zone afzonderlijk te bewaken.  Of voor open bare eind punten kunt u ze integreren met een product voor DNS-taak verdeling, zoals [Traffic Manager](../traffic-manager/traffic-manager-overview.md) en één DNS-naam gebruiken. De client wordt vervolgens omgezet in deze DNS-naam naar meerdere zonegebonden IP-adressen.  

Als u deze concepten (zone-redundante en zonegebonden voor dezelfde back-end) wilt laten door lopen, bekijkt u [meerdere frontends voor Azure Load Balancer](load-balancer-multivip-overview.md).

Voor een open bare Load Balancer frontend voegt u een para meter *zones* toe aan de open bare IP-resource waarnaar wordt verwezen door de frontend-IP-configuratie die wordt gebruikt door de betreffende regel.

Voor een interne Load Balancer frontend voegt u een para meter *zones* toe aan de interne Load Balancer frontend-IP-configuratie. De zonegebonden-frontend zorgt ervoor dat de Load Balancer een IP-adres in een subnet naar een specifieke zone te garanderen.

### <a name="cross-zone-load-balancing"></a>Taak verdeling in meerdere zones

Taak verdeling tussen meerdere zones is de mogelijkheid van Load Balancer om een back-end-eind punt in een zone te bereiken en is onafhankelijk van front-end en de bijbehorende zonality.  Elke regel voor taak verdeling kan een back-end-instantie in elke beschikbaarheids zone of regionale instanties richten.

U moet ervoor zorgen dat u uw scenario kunt bouwen op een manier die een begrip van beschikbaarheids zones uitmaakt. U moet bijvoorbeeld de implementatie van de virtuele machine in één zone of meerdere zones garanderen en zonegebonden front-end-en zonegebonden-back-endservers in dezelfde zone uitlijnen.  Als u beschikbaarheids zones met alleen zonegebonden-resources kruist, werkt het scenario wel, maar heeft dit mogelijk geen duidelijke fout modus ten opzichte van beschikbaarheids zones. 

### <a name="backend"></a>Back-end

Load Balancer werkt met exemplaren van virtuele machines.  Dit kunnen zelfstandige, beschikbaarheids sets of virtuele-machine schaal sets zijn.  Elk exemplaar van een virtuele machine in één virtueel netwerk kan deel uitmaken van de back-end-groep, ongeacht of het al dan niet is gegarandeerd voor een zone of waarop de zone is gegarandeerd.

Als u uw front-end en back-end met één zone wilt afstemmen en garanderen, plaatst u alleen virtuele machines binnen dezelfde zone in de bijbehorende back-end-pool.

Als u virtuele machines in meerdere zones wilt adresseren, plaatst u virtuele machines van meerdere zones in dezelfde back-endadresgroep.  Wanneer u schaal sets voor virtuele machines gebruikt, kunt u een of meer virtuele-machine schaal sets in dezelfde back-end-groep plaatsen.  En elk van deze schaal sets voor virtuele machines kunnen zich in één of meerdere zones bevinden.

### <a name="outbound-connections"></a>Uitgaande verbindingen

Dezelfde zone-redundante en zonegebonden eigenschappen zijn van toepassing op [uitgaande verbindingen](load-balancer-outbound-connections.md).  Een zone-redundant openbaar IP-adres dat wordt gebruikt voor uitgaande verbindingen, wordt door alle zones bediend. Een openbaar IP-adres van zonegebonden wordt alleen bediend door de zone waarin het wordt gegarandeerd.  Voor uitgaande verbinding met SNAT-poort toewijzingen blijft de zone storingen en uw scenario biedt geen uitgaande SNAT-connectiviteit als dit niet van invloed is op de zone storing.  Hiervoor moet u mogelijk verzen dingen of voor het opnieuw tot stand brengen van verbindingen voor zones-redundante scenario's hebben als een stroom wordt uitgevoerd door een betrokken zone.  Stromen in andere zones dan de betrokken zones worden niet beïnvloed.

Het algoritme voor de toewijzing van de SNAT-poort is hetzelfde met of zonder beschikbaarheids zones.

### <a name="health-probes"></a>Statuscontroles

De bestaande definities voor de Health-tests blijven behouden, zonder beschikbaarheids zones.  We hebben echter het status model op een infrastructuur niveau uitgebreid. 

Wanneer u gebruikmaakt van zone-redundante front-ends, wordt Load Balancer uitbrei ding van het interne status model om de bereik baarheid van een virtuele machine onafhankelijk te controleren vanuit elke beschikbaarheids zone en de paden af te sluiten in zones die mogelijk zijn mislukt zonder tussen komst van de klant.  Als een gegeven pad niet beschikbaar is in de Load Balancer-infra structuur van een zone naar een virtuele machine in een andere zone, kan Load Balancer deze fout detecteren en voor komen. Andere zones die deze VM kunnen bereiken, kunnen de virtuele machine blijven gebruiken vanaf hun respectieve frontends.  Als gevolg hiervan is het mogelijk dat tijdens de fout gebeurtenissen elke zone enigszins verschillende distributies van nieuwe stromen kan hebben en de algehele status van uw end-to-end-service beveiligt.

## <a name="design-considerations"></a><a name="design"></a>Ontwerp overwegingen

Load Balancer is in het algemeen flexibel in de context van beschikbaarheids zones. U kunt uitlijnen op zones of u kunt ervoor kiezen om zone-redundant te maken voor elke regel.  Verhoogde Beschik baarheid is de prijs van verhoogde complexiteit en u moet ontwerpen voor de beschik baarheid van optimale prestaties.  Laten we eens kijken naar enkele belang rijke aandachtspunten voor het ontwerp.

### <a name="automatic-zone-redundancy"></a>Automatische zone-redundantie

Load Balancer maakt het eenvoudig om één IP-adres te hebben als een zone-redundante front-end. Een zone-redundant IP-adres kan veilig worden gebruikt voor een zonegebonden-bron in elke zone en er kunnen een of meer zone fouten optreden, zolang de ene zone in orde blijft binnen de regio. Daarentegen is een zonegebonden-front-end een verlaging van de service naar een enkele zone en deelt het verspreidings niveau met de betreffende zone.

Zone-redundantie impliceert niet Hitless DataPath of Control-vlak;  het is uitdrukkelijk gegevens vlak. Zone-redundante stromen kunnen alle zones en de stromen van een klant gebruiken om alle zones in een regio te gebruiken. In het geval van een zone storing worden de verkeers stromen die op dat moment in goede zones worden gebruikt, niet beïnvloed.  Verkeers stromen die gebruikmaken van een zone op het moment van de zone storing, kunnen worden beïnvloed, maar toepassingen kunnen worden hersteld. Deze stromen kunnen in de resterende gezonde zones binnen de regio worden voortgezet na het opnieuw verzenden of opnieuw instellen, zodra Azure is geconvergeerd rond de zone storing.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a>Grenzen van meerdere zones

Het is belang rijk om te begrijpen dat elke keer dat een end-to-end-service zones overschrijdt, u verspreiding deelt met niet één zone, maar mogelijk meerdere zones.  Als gevolg hiervan is het mogelijk dat uw end-to-end-service geen Beschik baarheid heeft verkregen via niet-zonegebonden-implementaties.

Vermijd onbedoelde afhankelijkheden voor meerdere zones, waardoor de beschik baarheid wordt gerealiseerd bij het gebruik van beschikbaarheids zones.  Als uw toepassing bestaat uit meerdere onderdelen en u zich wilt voordoen aan een zone storing, moet u ervoor zorgen dat u voldoende essentiële onderdelen in het geval van een mislukte zone overhoudt.  Eén belang rijk onderdeel van uw toepassing kan bijvoorbeeld van invloed zijn op uw hele toepassing als deze alleen voor komt in een andere zone dan de nagelaten zone (s).  Daarnaast moet u ook rekening houden met het herstel van de zone en hoe uw toepassing convergeert. U moet weten hoe de redenen van uw toepassing zijn ten opzichte van fouten van delen van de toepassingen. Laten we eens kijken naar enkele belang rijke punten en gebruiken ze als inspiratie punt voor vragen zoals u in uw specifieke scenario kunt zien.

- Als uw toepassing twee onderdelen heeft, zoals een IP-adres en een virtuele machine met een beheerde schijf, en deze worden gegarandeerd in zone 1 en zone 2, wordt uw end-to-end-service niet overstaan wanneer zone 1 mislukt.  Vermijd zones met zonegebonden-scenario's tenzij u volledig begrijpt dat u een potentieel gevaarlijke fout modus maakt.  Dit scenario mag flexibiliteit bieden.

- Als uw toepassing twee onderdelen heeft, zoals een IP-adres en een virtuele machine met een beheerde schijf, en deze worden gegarandeerd zone-redundante en zone 1, gaat uw end-to-end-service niet over zone storingen van zone 2, zone 3 of beide, tenzij zone 1 is mislukt.  U verliest echter een mogelijke reden om de status van uw service te controleren als u er zeker van bent dat u de beschik baarheid van de front-end kunt bereiken.  Overweeg het ontwikkelen van een uitgebreidere status-en capaciteits model.  U kunt zone-redundante en zonegebonden-concepten samen gebruiken om inzicht en beheer baarheid uit te breiden.

- Als uw toepassing twee onderdelen heeft, zoals een zone-redundante Load Balancer frontend en een schaalset in meerdere zones in drie zones, zijn uw resources in zones die niet van invloed zijn op de fout, beschikbaar, maar kan uw end-to-end-service capaciteit worden verslechterd tijdens een storing in de zone. In het perspectief van een infra structuur kan uw implementatie een of meer zone storingen overleven, en dit leidt tot de volgende vragen:
  - Begrijpt u hoe de oorzaken van uw toepassingen van toepassing zijn op dergelijke fouten en de capaciteit van het bedrijf?
  - Moet u in uw service veiligheids maatregelen hebben om zo nodig een failover naar een regio paar af te dwingen?
  - Hoe kunt u een dergelijk scenario bewaken, detecteren en beperken? U kunt Standard Load Balancer diagnostische gegevens gebruiken om de bewaking van uw end-to-end-service prestaties te verbeteren. Bedenk wat er beschikbaar is en wat mogelijk moet worden uitgebreid voor een volledige foto.

- Zones kunnen fouten gemakkelijker te begrijpen en bevatten.  Zones mislukken echter niet anders dan andere fouten wanneer het gaat om concepten zoals time-outs, nieuwe pogingen en uitstel-algoritmen. Hoewel Azure Load Balancer voorziet in zone-redundante paden en probeert snel, op pakket niveau in realtime te herstellen, kunnen er tijdens het ontstaan van een storing opnieuw worden verzonden of hersteld. het is belang rijk om te begrijpen hoe uw toepassing met fouten kan omgaan. Uw taakverdelings schema blijft bewaard, maar u moet het volgende plannen:
  - Als er een storing optreedt in een zone, begrijpt uw end-to-end-service dit en als de status verloren gaat, hoe kunt u deze herstellen?
  - Wanneer een zone wordt geretourneerd, weet uw toepassing hoe u veilig kunt convergeren?

Bekijk de [ontwerp patronen voor Azure-Clouds](https://docs.microsoft.com/azure/architecture/patterns/) om de flexibiliteit van uw toepassing te verbeteren tot fouten.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Beschikbaarheidszones](../availability-zones/az-overview.md)
- Meer informatie over [Standard Load Balancer](load-balancer-standard-overview.md)
- Meer informatie over het [laden van virtuele machines binnen een zone met behulp van een Standard Load Balancer met een zonegebonden](load-balancer-standard-public-zonal-cli.md) -front-end
- Meer informatie over het verdelen [van virtuele machines over zones met behulp van een Standard Load Balancer met een zone-redundante front-end](load-balancer-standard-public-zone-redundant-cli.md)
- Meer informatie over [ontwerp patronen voor Azure-Clouds](https://docs.microsoft.com/azure/architecture/patterns/) voor het verbeteren van de tolerantie van uw toepassing tot fout scenario's.
