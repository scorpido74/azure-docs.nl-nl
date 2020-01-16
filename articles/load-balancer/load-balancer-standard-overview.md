---
title: Wat is Azure Standard Load Balancer?
titleSuffix: Azure Load Balancer
description: Met dit leer traject kunt u aan de slag met een overzicht van Azure Standard Load Balancer-functies.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: allensu
ms.openlocfilehash: b4db0746f64bfde04f09b589ac6beb6dc74d13f2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978684"
---
# <a name="azure-standard-load-balancer-overview"></a>Overzicht van Azure Standard Load Balancer

Met Azure Load Balancer kunt u uw toepassingen schalen en hoge Beschik baarheid maken voor uw services. Load Balancer kunnen worden gebruikt voor zowel inkomend als uitgaande scenario's en biedt een lage latentie, hoge door Voer en schaalbaar tot miljoenen stromen voor alle TCP-en UDP-toepassingen. 

Dit artikel is gericht op Standard Load Balancer.  Raadpleeg [Load Balancer-overzicht](load-balancer-overview.md) voor een meer algemeen overzicht van Azure Load Balancer.

## <a name="what-is-standard-load-balancer"></a>Wat is een Standard Load Balancer?

Standard Load Balancer is een nieuw Load Balancer product voor alle TCP-en UDP-toepassingen met een uitgebreidere en uitgebreidere functie die is ingesteld via basis Load Balancer.  Hoewel er veel overeenkomsten zijn, is het belang rijk om vertrouwd te raken met de verschillen zoals beschreven in dit artikel.

U kunt Standard Load Balancer gebruiken als een open bare of interne Load Balancer. Een virtuele machine kan worden verbonden met één open bare en een interne Load Balancer resource.

De functies van de Load Balancer resource worden altijd uitgedrukt als een front-end, een regel, een status test en een back-end-groeps definitie.  Een resource kan meerdere regels bevatten. U kunt virtuele machines in de back-end-pool plaatsen door de back-end-pool op te geven van de NIC-resource van de virtuele machine.  Deze para meter wordt door gegeven via het netwerk profiel en uitgevouwen bij het gebruik van virtuele-machine schaal sets.

Een belang rijke hoogte is het bereik van het virtuele netwerk van de bron.  Hoewel Basic Load Balancer bestaat binnen het bereik van een beschikbaarheidsset, is een Standard Load Balancer volledig geïntegreerd met het bereik van een virtueel netwerk en zijn alle concepten van het virtuele netwerk van toepassing.

Load Balancer resources zijn objecten waarin u kunt zien hoe Azure de multi tenant-infra structuur moet Program meren om het scenario dat u wilt maken te verhalen.  Er is geen rechtstreekse relatie tussen Load Balancer resources en de werkelijke infra structuur. Als u een Load Balancer maakt, wordt er geen exemplaar gemaakt, is de capaciteit altijd beschikbaar en zijn er geen vertragingen voor opstarten of schalen. 

### <a name="backend"></a>Back-end-groep

Standard Load Balancer back-end-Pools worden uitgebreid naar elke virtuele-machine bron in een virtueel netwerk.  Het kan Maxi maal 1000 back-end-exemplaren bevatten.  Een back-end-exemplaar is een IP-configuratie, een eigenschap van een NIC-resource.

De back-end-pool kan zelfstandige virtuele machines, beschikbaarheids sets of virtuele-machine schaal sets bevatten.  U kunt ook resources in de back-end-pool combi neren. U kunt Maxi maal 150 resources in de back-end-pool combi neren per Load Balancer resource.

Wanneer u overweegt om uw back-end-pool te ontwerpen, kunt u het minste aantal afzonderlijke back-endservers bronnen ontwerpen om de duur van beheer bewerkingen verder te optimaliseren.  Er is geen verschil in de prestaties of schaal van het gegevens vlak.

### <a name="probes"></a>Status controles
  
Standard Load Balancer voegt ondersteuning toe voor [https-status tests](load-balancer-custom-probe-overview.md#httpprobe) (http-test met Transport Layer Security (TLS) wrapper) om uw https-toepassingen nauw keurig te controleren.  

Wanneer de volledige back-end [-groep wordt gecontroleerd, worden alle](load-balancer-custom-probe-overview.md#probedown)ingestelde TCP-verbindingen bovendien door Standard Load Balancer toegestaan. (Basic Load Balancer worden alle TCP-verbindingen met alle instanties beëindigd).

Bekijk [Load Balancer status tests](load-balancer-custom-probe-overview.md) voor meer informatie.

### <a name="az"></a>Beschikbaarheidszones

>[!IMPORTANT]
>Bekijk [Beschikbaarheidszones](../availability-zones/az-overview.md) voor Verwante onderwerpen, inclusief alle specifieke informatie over de regio.

Standard Load Balancer ondersteunt extra mogelijkheden in regio's waar Beschikbaarheidszones beschikbaar zijn.  Deze functies zijn incrementeel voor alle Standard Load Balancer.  Beschikbaarheidszones configuraties beschikbaar zijn voor open bare en interne Standard Load Balancer.

Niet-zonegebonden front-ends worden standaard zone-redundant wanneer ze in een regio met Beschikbaarheidszones worden geïmplementeerd.   Een zone-redundante front-end bevindt zich in de zone storing en wordt door de toegewezen infra structuur in alle zones tegelijk bediend. 

Daarnaast kunt u een frontend garanderen voor een specifieke zone. Een zonegebonden-front-end-shares worden meegenomen met de betreffende zone en worden alleen geleverd door een toegewezen infra structuur in één zone.

Taak verdeling in meerdere zones is beschikbaar voor de back-end-groep en alle virtuele-machine bronnen in een vnet kunnen deel uitmaken van een back-end-groep.

Bekijk de [gedetailleerde beschrijving van Beschikbaarheidszones gerelateerde vaardig heden](load-balancer-standard-availability-zones.md).

### <a name="diagnostics"></a>Diagnostische gegevens

Standard Load Balancer biedt multidimensionale metrische gegevens via Azure Monitor.  Deze metrische gegevens kunnen worden gefilterd, gegroepeerd en uitgesplitst voor een bepaalde dimensie.  Ze bieden actuele en historische inzichten in de prestaties en status van uw service.  Resource Health wordt ook ondersteund.  Hieronder vindt u een beknopt overzicht van de ondersteunde diagnostische gegevens:

| Gegevens | Beschrijving |
| --- | --- |
| VIP-Beschik baarheid | Standard Load Balancer doorlopend het gegevenspad van binnen een regio naar de Load Balancer front-end naar de SDN-stack die uw virtuele machine ondersteunt. Zolang de gezonde instanties blijven bestaan, volgt de meting hetzelfde pad als het verkeer met gelijke taak verdeling van uw toepassing. Het gegevenspad dat door uw klanten wordt gebruikt, wordt ook gevalideerd. De meting is onzichtbaar voor uw toepassing en heeft geen invloed op andere bewerkingen.|
| DIP-Beschik baarheid | Standard Load Balancer maakt gebruik van een gedistribueerde Health-Service die de status van uw toepassings eindpunt bewaakt volgens de configuratie-instellingen. Deze metriek levert een gefilterde combi natie van of per eind punt-weer gave van elk eind punt van de afzonderlijke instantie in de Load Balancer groep.  U kunt zien hoe Load Balancer de status van uw toepassing weergeeft, zoals wordt aangegeven door de configuratie van uw Health probe.
| SYN-pakketten | Standard Load Balancer beëindigt geen TCP-verbindingen of communiceert niet met TCP-of UDP-pakket stromen. Stromen en hun hand shakes zijn altijd tussen de bron-en de VM-instantie. Voor een betere probleem oplossing van uw TCP-protocol scenario's kunt u de tellers van SYN-pakketten gebruiken om te begrijpen hoeveel TCP-verbindings pogingen er worden gedaan. De metriek rapporteert het aantal TCP SYN-pakketten dat is ontvangen.|
| SNAT-verbindingen | Standard Load Balancer rapporteert het aantal uitgaande stromen dat is gemaskerd voor de front-end van het open bare IP-adres. SNAT-poorten zijn een exhaustible-resource. Met deze metriek kan worden aangegeven hoe sterk uw toepassing vertrouwt op SNAT voor uitgaande stroom stromen.  Tellers voor geslaagde en mislukte uitgaande SNAT-stromen worden gerapporteerd en kunnen worden gebruikt om problemen op te lossen en de status van uw uitgaande stromen te begrijpen.|
| Byte tellers | Standard Load Balancer rapporteert de verwerkte gegevens per front-end.|
| Pakket items | Standard Load Balancer rapporteert de verwerkte pakketten per front-end.|

Raadpleeg de [gedetailleerde beschrijving van Standard Load Balancer diagnose](load-balancer-standard-diagnostics.md).

### <a name="haports"></a>HA-poorten

Standard Load Balancer ondersteunt een nieuw type regel.  

U kunt regels voor taak verdeling configureren om de schaal van uw toepassing te schalen en zeer betrouwbaar te maken. Wanneer u een regel voor taak verdeling van HA-poorten gebruikt, levert Standard Load Balancer per stroom taak verdeling op elke tijdelijke poort van het front-end-IP-adres van een interne Standard Load Balancer.  De functie is handig voor andere scenario's waarbij het niet praktisch is of niet wenselijk is om afzonderlijke poorten op te geven.

Met een taakverdelings regel voor HA-poorten kunt u Active-passieve of actief-actief n + 1-scenario's maken voor virtuele netwerk apparaten en voor elke toepassing die grote bereiken aan binnenkomende poorten vereist.  Een status test kan worden gebruikt om te bepalen welke back-ends nieuwe stromen moeten ontvangen.  U kunt een netwerk beveiligings groep gebruiken om een scenario met een poort bereik te emuleren.

>[!IMPORTANT]
> Als u van plan bent om een virtueel netwerk apparaat te gebruiken, raadpleegt u uw leverancier voor meer informatie over of het product is getest met HA-poorten en volgt u de specifieke richt lijnen voor de implementatie. 

Bekijk de [gedetailleerde bespreking van ha-poorten](load-balancer-ha-ports-overview.md).

### <a name="securebydefault"></a>Standaard beveiligd

Standard Load Balancer is volledig onboarding voor het virtuele netwerk.  Het virtuele netwerk is een privé, gesloten netwerk.  Omdat standaard load balancers en standaard open bare IP-adressen zijn ontworpen om dit virtuele netwerk vanaf buiten het virtuele netwerk toegankelijk te maken, worden deze resources nu standaard gesloten, tenzij u ze opent. Dit betekent dat er nu netwerk beveiligings groepen (Nsg's) worden gebruikt om toegestaan verkeer expliciet toe te staan en te white list.  U kunt uw hele virtuele Data Center maken en zelf bepalen wat en wanneer het beschikbaar moet zijn.  Als u geen NSG op een subnet of NIC van uw virtuele-machine bron hebt, mag het verkeer deze bron niet bereiken.

Zie [netwerk beveiligings groepen](../virtual-network/security-overview.md)voor meer informatie over nsg's en hoe u deze kunt Toep assen voor uw scenario.

### <a name="outbound"></a>Uitgaande verbindingen

Load Balancer ondersteunt de scenario's voor inkomend en uitgaand verkeer.  Standard Load Balancer wijkt aanzienlijk af van de basis Load Balancer met betrekking tot uitgaande verbindingen.

Bron netwerk adres omzetting (SNAT) wordt gebruikt voor het toewijzen van interne, privé-IP-adressen in uw virtuele netwerk aan open bare IP-adressen op Load Balancer-front-end.

Standard Load Balancer introduceert een nieuw algoritme voor een [robuuster, schaalbaar en voorspelbaar SNAT-algoritme](load-balancer-outbound-connections.md#snat) , en maakt nieuwe mogelijkheden mogelijk, verwijdert dubbel zinnigheid en dwingt expliciete configuraties af in plaats van neven effecten. Deze wijzigingen zijn nood zakelijk om ervoor te zorgen dat er nieuwe functies zijn. 

Dit zijn de belangrijkste grond beginselen die u kunt onthouden wanneer u met Standard Load Balancer werkt:

- door de voltooiing van een regel wordt de Load Balancer resource gestationeerd.  alle Program meren van Azure is afgeleid van de configuratie.
- Wanneer er meerdere frontends beschikbaar zijn, worden alle frontends gebruikt en elke frontend vermenigvuldigt het aantal beschik bare SNAT-poorten
- u kunt kiezen en bepalen of u niet wilt dat een bepaalde frontend voor uitgaande verbindingen wordt gebruikt.
- uitgaande scenario's zijn expliciete en uitgaande connectiviteit bestaat niet totdat deze is opgegeven.
- taakverdelings regels bepalen hoe SNAT wordt geprogrammeerd. Taakverdelings regels zijn specifiek voor het protocol. SNAT is protocol-specifiek en configuratie moet weer spie gelen in plaats van een neven effect te maken.

#### <a name="multiple-frontends"></a>Meerdere frontends
Als u meer SNAT-poorten verwacht of als er al een hoge vraag voor uitgaande verbindingen optreedt, kunt u ook incrementele SNAT-poort inventaris toevoegen door extra frontends, regels en back-endservers op dezelfde virtuele machine te configureren. resources.

#### <a name="control-which-frontend-is-used-for-outbound"></a>Bepalen welke frontend wordt gebruikt voor uitgaande verbindingen
Als u uitgaande verbindingen wilt beperken tot alleen afkomstig van een specifiek frontend-IP-adres, kunt u de uitgaande SNAT uitschakelen op de regel die de uitgaande toewijzing uitdrukt.

#### <a name="control-outbound-connectivity"></a>Uitgaande connectiviteit beheren
Standard Load Balancer bestaat in de context van het virtuele netwerk.  Een virtueel netwerk is een geïsoleerd particulier netwerk.  Tenzij er een koppeling met een openbaar IP-adres bestaat, is de open bare verbinding niet toegestaan.  U kunt de [VNet-service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md) bereiken omdat ze zich in en lokaal voor uw virtuele netwerk bevinden.  Als u een uitgaande verbinding wilt maken met een bestemming buiten uw virtuele netwerk, hebt u twee opties:
- Wijs een openbaar IP-adres van een standaard-SKU toe als openbaar IP-adres op exemplaar niveau voor de virtuele-machine bron of
- plaats de bron van de virtuele machine in de back-endadresgroep van een open bare Standard Load Balancer.

Beide zorgt ervoor dat uitgaande verbindingen van het virtuele netwerk naar buiten het virtuele netwerk. 

Als u _alleen_ een interne Standard Load Balancer hebt gekoppeld aan de back-end-groep waarin de virtuele-machine bron zich bevindt, kan uw virtuele machine alleen virtuele netwerk bronnen en [VNet-service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md)bereiken.  U kunt de stappen in de vorige alinea volgen om uitgaande connectiviteit te maken.

De uitgaande verbinding van een virtuele-machine resource die niet is gekoppeld aan standaard-Sku's, blijft net zo lang.

Bekijk de [gedetailleerde beschrijving van uitgaande verbindingen](load-balancer-outbound-connections.md).

### <a name="multife"></a>Meerdere frontends
Load Balancer ondersteunt meerdere regels met meerdere frontends.  Standard Load Balancer breidt deze uit naar uitgaande scenario's.  Uitgaande scenario's zijn in feite de inverse van een regel voor binnenkomende taak verdeling.  Met de regel voor inkomende taak verdeling wordt ook een koppeling voor uitgaande verbindingen gemaakt. Standard Load Balancer gebruikt alle frontends die zijn gekoppeld aan een virtuele-machine bron via een taakverdelings regel.  Daarnaast kunt u met een para meter voor de taakverdelings regel een taakverdelings regel voor de doel einden van de uitgaande connectiviteit onderdrukken, waardoor de selectie van specifieke front-ends inclusief geen kan worden onderdrukt.

Voor een vergelijking selecteert basis Load Balancer één wille keurige front-end en er is geen mogelijkheid om te bepalen welk item is geselecteerd.

Bekijk de [gedetailleerde beschrijving van uitgaande verbindingen](load-balancer-outbound-connections.md).

### <a name="operations"></a>Beheer bewerkingen

Standard Load Balancer resources bestaan op een volledig nieuw infrastructuur platform.  Dit maakt snellere beheer bewerkingen mogelijk voor standaard-Sku's en voltooiings tijden, meestal minder dan 30 seconden per standaard SKU-resource.  Naarmate de back-endservers groter worden, neemt de duur van back-Pools ook toe.

U kunt Standard Load Balancer resources wijzigen en een standaard openbaar IP-adres van de ene virtuele machine nog veel sneller verplaatsen.

## <a name="migration-between-skus"></a>Migratie tussen Sku's

Sku's zijn niet onveranderbaar. Volg de stappen in deze sectie om van de ene resource-SKU naar een andere te gaan.

>[!IMPORTANT]
>Lees dit document in zijn geheel om inzicht te krijgen in de verschillen tussen Sku's en uw scenario zorgvuldig te hebben bestudeerd.  Mogelijk moet u extra wijzigingen aanbrengen om uw scenario af te stemmen.

### <a name="migrate-from-basic-to-standard-sku"></a>Migreren van Basic naar standaard-SKU

1. Maak indien nodig een nieuwe standaard resource (Load Balancer en open bare Ip's). Maak uw regels en test definities opnieuw.  Als u eerder een TCP-test naar 443/TCP gebruikt, overweeg dan om dit test protocol te wijzigen in een HTTPS-test en een pad toe te voegen.

2. Maak een nieuwe of werk bestaande NSG op NIC of subnet bij om verkeer met gelijke taak verdeling, test en andere verkeer dat u wilt toestaan, te white list.

3. Verwijder de Basic SKU-resources (Load Balancer en open bare Ip's, indien van toepassing) van alle VM-exemplaren. Zorg er ook voor dat alle VM-exemplaren van een beschikbaarheidsset worden verwijderd.

4. Koppel alle VM-exemplaren aan de nieuwe standaard SKU-resources.

>[!IMPORTANT]
>
>Overeenkomende Sku's moeten worden gebruikt voor Load Balancer en open bare IP-resources. U kunt geen combi natie van basis-SKU-resources en standaard-SKU-resources hebben. Het is evenmin mogelijk om zelfstandige virtuele machines, virtuele machines in een resource van een beschikbaarheidsset of resources uit schaalset met virtuele machines op beide SKU's tegelijk in te stellen.

## <a name="region-availability"></a>Regionale beschikbaarheid

Standard Load Balancer is momenteel beschikbaar in alle Azure-regio's.

## <a name="sla"></a>SLA 

Standaard load balancers zijn beschikbaar met een SLA van 99,99%.  Raadpleeg de [Standard Load Balancer-Sla](https://aka.ms/lbsla) voor meer informatie. 

## <a name="pricing"></a>Prijzen 

Gebruik van Standard Load Balancer wordt in rekening gebracht. 

- Aantal geconfigureerde load-balancing- en uitgaande regels (inkomende NAT-regels worden niet meegeteld in het totale aantal regels) 
- Hoeveelheid verwerkte inkomende en uitgaande gegevens, ongeacht de regel.

Ga naar de pagina [Prijs van Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/) voor informatie over de prijs van Standard Load Balancer.

## <a name="limitations"></a>Beperkingen

- Sku's zijn niet onveranderbaar. U kunt de SKU van een bestaande resource niet wijzigen.
- Een zelfstandige resource voor de virtuele machine, de resource van de beschikbaarheidsset of de virtuele-machine schaalset kan verwijzen naar één SKU, nooit beide.
- Een Load Balancer regel kan niet twee virtuele netwerken omvatten.  Front-end en hun bijbehorende back-end-instanties moeten zich in hetzelfde virtuele netwerk bevinden.  
- Het [verplaatsen van abonnements bewerkingen](../azure-resource-manager/management/move-resource-group-and-subscription.md) wordt niet ondersteund voor Standard SKU lb-en PIP-resources.
- Rollen voor webwerkers zonder VNet en andere micro soft-platform Services kunnen toegankelijk zijn vanaf exemplaren achter alleen een intern Standard Load Balancer als gevolg van een neven effect van de werking van de functies prevnet-Services en andere platform Services. U moet er niet op vertrouwen dat u deze kunt gebruiken als de respectieve service zelf of het onderliggende platform kan zonder kennisgeving worden gewijzigd. U moet altijd aannemen dat u een [uitgaande connectiviteit](load-balancer-outbound-connections.md) expliciet moet maken als u alleen een interne Standard Load Balancer wilt gebruiken.
- Load Balancer is een TCP- of UDP-product voor taakverdeling en Port Forwarding voor deze specifieke IP-protocollen.  Taakverdelingsregels en inkomende NAT-regels worden ondersteund voor TCP en UDP en worden niet ondersteund voor andere IP-protocollen, met inbegrip van ICMP. Load Balancer beëindigt de nettolading van een UDP- of TCP-stroom niet, reageert er niet op of communiceert er op geen enkele andere ander manier mee. Het is geen proxy. De validatie van de connectiviteit met een front-end moet in-band worden uitgevoerd met hetzelfde protocol dat wordt gebruikt in een taak verdeling of een binnenkomende NAT-regel (TCP of UDP) _en_ ten minste één van de virtuele machines moet een reactie genereren voor een client om een reactie van een front-end te zien.  Er wordt geen in-band-antwoord ontvangen van de Load Balancer front-end geeft aan dat er geen virtuele machines kunnen reageren.  Het is niet mogelijk om te communiceren met een Load Balancer front-end zonder dat er een virtuele machine kan reageren.  Dit geldt ook voor uitgaande verbindingen waar [poortmaskering SNAT](load-balancer-outbound-connections.md#snat) alleen wordt ondersteund voor TCP en UDP; andere IP-protocollen, waaronder ICMP, werken ook niet.  Wijs een openbaar IP-adres op instantieniveau toe om dit op te lossen.
- In tegens telling tot open bare load balancers die [uitgaande verbindingen](load-balancer-outbound-connections.md) bieden bij het overstappen van privé-IP-adressen in het virtuele netwerk naar open bare IP-adressen, worden met interne load balancers geen uitgaande verlopende verbindingen omgezet naar de front-end van een interne Load Balancer als beide zich in particuliere IP-adres ruimte bevinden.  Zo voor komt u dat er geen SNAT-uitputting binnen de unieke interne IP-adres ruimte is, waarbij omzetting niet vereist is.  Het gevolg is dat als een uitgaande stroom van een virtuele machine in de back-end-pool een stroom probeert te maken van een front-end van de interne Load Balancer waarin de groep zich bevindt _en_ aan zichzelf wordt toegewezen, beide zijden van de stroom niet overeenkomen en de stroom niet kan worden uitgevoerd.  Als de stroom niet opnieuw is toegewezen aan dezelfde virtuele machine in de back-end-pool, waardoor de stroom naar de front-end is gemaakt, slaagt de stroom.   Wanneer de stroom wordt teruggeleid naar zichzelf, lijkt de uitgaande stroom van de virtuele machine naar de front-end en de bijbehorende binnenkomende stroom lijkt afkomstig van de virtuele machine naar zichzelf. Vanuit het oogpunt van het gastbesturingssysteem gezien, komen de inkomende en uitgaande delen van dezelfde flow in de virtuele machine niet overeen. De TCP-stack herkent deze niet als twee helften van dezelfde stroom, omdat de bron en de bestemming niet overeenkomen.  Wanneer de stroom wordt toegewezen aan een andere virtuele machine in de back-end-pool, worden de helften van de stroom vergeleken en kan de virtuele machine reageren op de stroom.  Het symptoom voor dit scenario is periodieke verbindingstime-outs. Er zijn verschillende veelvoorkomende oplossingen voor het op een betrouw bare manier bereiken van dit scenario (die afkomstig zijn van een back-end-pool naar de back-end-Pools die respectievelijk intern zijn Load Balancer front-end), waaronder het invoegen van een proxy van een derde partij achter de interne Load Balancer of het gebruik van de [DSR-stijl regels](load-balancer-multivip-overview.md).  Als oplossing hiervoor zou u een openbare Load Balancer kunnen gebruiken, maar het scenario dat daar het resultaat van is, is gevoelig voor [SNAT-uitputting](load-balancer-outbound-connections.md#snat) en moet worden vermeden, tenzij dit nauwkeurig wordt beheerd.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Load Balancer](load-balancer-overview.md).
- Meer informatie over het gebruik van [Standard Load Balancer en Beschikbaarheidszones](load-balancer-standard-availability-zones.md).
- Meer informatie over [status controles](load-balancer-custom-probe-overview.md).
- Meer informatie over [Standard Load Balancer diagnostische gegevens](load-balancer-standard-diagnostics.md).
- Meer informatie over [ondersteunde multi-dimensionale metrische gegevens](../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) voor diagnostische gegevens in [Azure monitor](../monitoring-and-diagnostics/monitoring-overview.md).
- Meer informatie over het gebruik van [Load Balancer voor uitgaande verbindingen](load-balancer-outbound-connections.md).
- Meer informatie over [Uitgaande regels](load-balancer-outbound-rules-overview.md).
- Meer informatie over [TCP Reset bij niet-actief](load-balancer-tcp-reset.md).
- Meer informatie over Standard Load Balancer met de taakverdelings [regels van ha-poorten](load-balancer-ha-ports-overview.md).
- Meer informatie over het gebruik van [Load Balancer met meerdere frontends](load-balancer-multivip-overview.md).
- Meer informatie over [netwerk beveiligings groepen](../virtual-network/security-overview.md).
