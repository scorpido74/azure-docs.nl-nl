---
title: Problemen met Azure Virtual Network NAT-connectiviteit oplossen
titleSuffix: Azure Virtual Network NAT troubleshooting
description: Problemen met Virtual Network NAT oplossen.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2020
ms.author: allensu
ms.openlocfilehash: c629b3425cd095a6ac9d305b5cd6de58ed9d572a
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674332"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Problemen met Azure Virtual Network NAT-connectiviteit oplossen

Dit artikel helpt beheerders bij het vaststellen en oplossen van verbindings problemen bij het gebruik van Virtual Network NAT.

>[!NOTE] 
>Virtual Network NAT is op dit moment beschikbaar als open bare preview. Het is momenteel alleen beschikbaar in een beperkt aantal [regio's](nat-overview.md#region-availability). Deze preview is beschikbaar zonder service level agreement en wordt niet aanbevolen voor productie werkbelastingen. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="problems"></a>Problemen

* [SNAT-uitputting](#snat-exhaustion)
* [ICMP-ping is mislukt](#icmp-ping-is-failing)
* [Connectiviteits fouten](#connectivity-failures)
* [IPv6-samen werking](#ipv6-coexistence)

Volg de stappen in de volgende sectie om deze problemen op te lossen.

## <a name="resolution"></a>Oplossing

### <a name="snat-exhaustion"></a>SNAT-uitputting

Eén [NAT-gateway resource](nat-gateway-resource.md) ondersteunt van 64.000 tot 1.000.000 gelijktijdige stromen.  Elk IP-adres levert een 64.000 SNAT-poort aan de beschik bare inventaris. U kunt Maxi maal 16 IP-adressen per NAT-gateway Resource gebruiken.  Het SNAT-mechanisme wordt [hier](nat-gateway-resource.md#source-network-address-translation) uitgebreid beschreven.

De hoofd oorzaak van de SNAT-uitputting is vaak een anti patroon voor de manier waarop uitgaande connectiviteit tot stand wordt gebracht en beheerd.  Lees deze sectie aandachtig.

#### <a name="steps"></a>Stappen

1. Onderzoek hoe uw toepassing uitgaande connectiviteit maakt (bijvoorbeeld code controle of pakket opname). 
2. Bepaal of deze activiteit verwacht gedrag is of of de toepassing niet goed werkt.  Gebruik [metrische gegevens](nat-metrics.md) in azure monitor om uw bevindingen te staven. Gebruik de categorie ' mislukt ' voor de metrische gegevens voor de SNAT-verbinding.
3. Evalueren of de juiste patronen worden gevolgd.
4. Evalueren of de SNAT-poort uitputting moet worden verminderd met extra IP-adressen die zijn toegewezen aan de NAT gateway-resource.

#### <a name="design-patterns"></a>Ontwerppatronen

Maak waar mogelijk altijd gebruik van verbindings hergebruik en verbindings Pools.  Deze patronen voor komen problemen met de bron uitputting en leiden tot voorspelbaar, betrouwbaar en schaalbaar gedrag. Primitieven voor deze patronen vindt u in veel ontwikkel bibliotheken en frameworks.

_**Oplossing:**_ Juiste patronen gebruiken

- Denk aan [asynchrone polling-patronen](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) voor langlopende bewerkingen om verbindings bronnen vrij te maken voor andere bewerkingen.
- Lange levens stromen (bijvoorbeeld hergebruikte TCP-verbindingen) moeten TCP-keepalives of keepalives van de toepassingslaag gebruiken om time-out van tussenliggende systemen te voor komen.
- Geslaagde [patronen voor opnieuw proberen](https://docs.microsoft.com/azure/architecture/patterns/retry) moeten worden gebruikt om agressieve pogingen/bursts te voor komen tijdens tijdelijke storingen of herstel na storingen.
Het maken van een nieuwe TCP-verbinding voor elke HTTP-bewerking (ook wel ' atomische verbindingen ' genoemd) is een anti-patroon.  Met atomische verbindingen wordt voor komen dat uw toepassing goed kan worden geschaald en verspilt resources.  Meerdere bewerkingen in een pijp lijn altijd in dezelfde verbinding.  Uw toepassing zal profiteren van de transactie snelheid en resource kosten.  Als uw toepassing gebruikmaakt van trans port Layer encryption (bijvoorbeeld TLS), zijn er aanzienlijke kosten verbonden aan de verwerking van nieuwe verbindingen.  Bekijk de [ontwerp patronen voor Azure-Clouds](https://docs.microsoft.com/azure/architecture/patterns/) voor aanvullende best practice patronen.

#### <a name="possible-mitigations"></a>Mogelijke oplossingen

_**Oplossing:**_ Schaal de uitgaande verbinding als volgt:

| Scenario | Voorgelegd |Oplossing |
|---|---|---|
| U ondervindt conflicten voor SNAT-poorten en SNAT-poort uitputting tijdens peri Oden met een hoog gebruik. | De categorie ' mislukt ' voor de [Azure monitor van de SNAT-verbindingen bevat](nat-metrics.md) tijdelijke of permanente storingen in de loop van de tijd en het hoge verbindings volume.  | Bepaal of u meer open bare IP-adres bronnen of open bare IP-prefix bronnen kunt toevoegen. Met deze toevoeging kunnen Maxi maal 16 IP-adressen in totaal naar uw NAT-gateway worden toegevoegd. Deze toevoeging biedt meer inventarisatie voor de beschik bare SNAT-poorten (64.000 per IP-adres) en stelt u in staat om uw scenario verder te schalen.|
| U hebt al 16 IP-adressen opgegeven en er is nog steeds een SNAT-poort uitgeput. | Poging om extra IP-adres toe te voegen is mislukt. Het totale aantal IP-adressen van open bare IP-adres bronnen of open bare IP-voor voegsel resources overschrijdt het totaal van 16. | Distribueer uw toepassings omgeving over meerdere subnetten en geef een NAT-gateway bron op voor elk subnet.  Evalueer uw ontwerp patroon (en) opnieuw om te optimaliseren op basis van voor gaande [richt lijnen](#design-patterns). |

>[!NOTE]
>Het is belang rijk om te begrijpen waarom de uitputting van de SNAT optreedt. Zorg ervoor dat u de juiste patronen gebruikt voor schaal bare en betrouw bare scenario's.  Het toevoegen van meer SNAT-poorten aan een scenario zonder dat de oorzaak van de vraag een laatste redmiddel moet zijn. Als u geen inzicht krijgt in waarom uw scenario druk op de SNAT-poort inventariseert, kunt u meer SNAT-poorten toevoegen aan de inventaris door meer IP-adressen toe te voegen, waardoor er slechts één uitputtings fout optreedt wanneer uw toepassing wordt geschaald.  U kunt andere inefficiënties en anti-patronen maskeren.

### <a name="icmp-ping-is-failing"></a>ICMP-ping is mislukt

[Virtual Network NAT](nat-overview.md) ondersteunt IPv4 UDP-en TCP-protocollen. ICMP wordt niet ondersteund en wordt naar verwachting mislukt.  

_**Oplossing:**_ Gebruik in plaats daarvan TCP-verbindings tests (bijvoorbeeld ' TCP ping ') en UDP-specifieke Application Layer-tests om end-to-end-verbindingen te valideren.

De volgende tabel kan worden gebruikt als uitgangs punt voor de hulpprogram ma's die u wilt gebruiken om tests te starten.

| Besturingssysteem | Algemene TCP-verbindings test | Test van de TCP-toepassingslaag | UDP |
|---|---|---|---|
| Linux | NC (algemene verbindings test) | krul (test voor TCP-toepassingslaag) | toepassingsspecifieke |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | Power shell [invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | toepassingsspecifieke |

### <a name="connectivity-failures"></a>Connectiviteits fouten

Connectiviteits problemen met [Virtual Network NAT](nat-overview.md) kunnen worden veroorzaakt door verschillende problemen:

* tijdelijke of permanente [SNAT-uitputting](#snat-exhaustion) van de NAT-gateway,
* tijdelijke fouten in de Azure-infra structuur, 
* tijdelijke fouten in het pad tussen Azure en de open bare Internet bestemming 
* tijdelijke of blijvende storingen op de open bare Internet bestemming.

Gebruik hulpprogram ma's zoals het volgende voor het valideren van de verbinding. [ICMP ping wordt niet ondersteund](#icmp-ping-is-failing).

| Besturingssysteem | Algemene TCP-verbindings test | Test van de TCP-toepassingslaag | UDP |
|---|---|---|---|
| Linux | NC (algemene verbindings test) | krul (test voor TCP-toepassingslaag) | toepassingsspecifieke |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | Power shell [invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | toepassingsspecifieke |

#### <a name="snat-exhaustion"></a>SNAT-uitputting

Raadpleeg de sectie over de [SNAT-uitputting](#snat-exhaustion) in dit artikel.

#### <a name="azure-infrastructure"></a>Azure-infrastructuur

Hoewel Azure de infra structuur met fantastische controle bewaakt en exploiteert, kunnen er tijdelijke fouten optreden omdat er geen garantie is dat de verzen ding zonder verlies is.  Gebruik ontwerp patronen waarmee SYN-herverzendingen voor TCP-toepassingen worden toegestaan. Gebruik verbindingstime-outs groot genoeg om het opnieuw verzenden van TCP SYN toe te staan om tijdelijke gevolgen te beperken die worden veroorzaakt door een verloren SYN-pakket.

_**Oplossen**_

* Controleer op [SNAT-uitputting](#snat-exhaustion).
* De configuratie parameter in een TCP-stack waarmee het gedrag van SYN-verzen ding wordt beheerd, wordt RTO ([time-out voor herverzending](https://tools.ietf.org/html/rfc793)) genoemd. De RTO-waarde is aanpasbaar maar meestal 1 seconde of hoger, standaard met exponentiële back-out.  Als de verbindingstime-out van uw toepassing te kort is (bijvoorbeeld 1 seconde), ziet u mogelijk sporadische verbindingstime-outs.  Verhoog de time-out voor de verbinding van de toepassing.
* Als u meer wilt weten, onverwachte time-outs met standaard gedrag van toepassingen, opent u een ondersteunings aanvraag voor verdere probleem oplossing.

Het wordt niet aanbevolen om de time-out voor de TCP-verbinding kunst matig te verlagen of de RTO-para meter af te stemmen.

#### <a name="public-internet-transit"></a>openbaar Internet doorvoer

De kans op tijdelijke fouten neemt toe met een langer pad naar het doel en meer tussenliggende systemen. Er wordt verwacht dat tijdelijke storingen de frequentie van [Azure-infra structuur](#azure-infrastructure)kunnen verhogen. 

Volg dezelfde richt lijnen als voor gaande [Azure-infrastructuur](#azure-infrastructure) sectie.

#### <a name="internet-endpoint"></a>Internet-eind punt

De voor gaande secties zijn van toepassing naast overwegingen met betrekking tot het Internet-eind punt waarmee uw communicatie tot stand is gebracht. Andere factoren die invloed kunnen hebben op succes van de verbinding zijn:

* verkeers beheer aan de kant van de bestemming, inclusief
- Beperking van API-frequentie die is opgelegd door de doel zijde
- Volumetrische DDoS-oplossingen of trans port Layer Traffic Shaping
* firewall of andere onderdelen op het doel 

Normaal gesp roken worden pakket opnames bij de bron en de bestemming (indien beschikbaar) vereist om te bepalen wat er gebeurt.

_**Oplossen**_

* Controleer op [SNAT-uitputting](#snat-exhaustion). 
* Valideer de verbinding met een eind punt in dezelfde regio of elders ter vergelijking.  
* Als u een hoog volume-of transactie verwerkings tempo maakt, moet u verkennen als het aantal fouten vermindert door te verminderen.
* Als het wijzigen van de frequentie invloed heeft op de frequentie van fouten, controleert u of de API-frequentie limieten of andere beperkingen voor de doel zijde mogelijk zijn bereikt.
* Als uw onderzoek onduidelijk is, opent u een ondersteunings aanvraag voor verdere probleem oplossing.

#### <a name="tcp-resets-received"></a>TCP-opnieuw instellen ontvangen

Als u TCP-opnieuw instellen (eerste TCP-pakketten) bekijkt die op de bron-VM zijn ontvangen, kunnen deze worden gegenereerd door de NAT-gateway op de privé zijde voor stromen die niet worden herkend als in uitvoering.  Een mogelijke reden is dat er een time-out is opgetreden voor de TCP-verbinding.  U kunt de time-out voor inactiviteit aanpassen van 4 minuten tot 120 minuten.

TCP-opnieuw instellen worden niet gegenereerd aan de open bare kant van NAT gateway-resources. Als u TCP-opnieuw instellen op de doel zijde ontvangt, worden deze gegenereerd door de stack van de bron-VM en niet door de NAT-gateway bron.

_**Oplossen**_

* Bekijk aanbevelingen voor [ontwerp patronen](#design-patterns) .  
* Open een ondersteunings aanvraag voor het oplossen van problemen, indien nodig.

### <a name="ipv6-coexistence"></a>IPv6-samen werking

[Virtual Network NAT](nat-overview.md) ondersteunt IPv4 UDP-en TCP-protocollen en implementatie in een [subnet met IPv6-voor voegsel wordt niet ondersteund](nat-overview.md#limitations).

_**Oplossing:**_ Implementeer NAT gateway op een subnet zonder IPv6-voor voegsel.

U kunt interesses aanduiden in aanvullende mogelijkheden via [Virtual Network NAT UserVoice](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Virtual Network NAT](nat-overview.md)
* Meer informatie over de [NAT gateway-resource](nat-gateway-resource.md)
* Meer informatie over [metrische gegevens en waarschuwingen voor NAT-gateway bronnen](nat-metrics.md).
* [Vertel ons wat u nu kunt bouwen voor Virtual Network nat in UserVoice](https://aka.ms/natuservoice).

