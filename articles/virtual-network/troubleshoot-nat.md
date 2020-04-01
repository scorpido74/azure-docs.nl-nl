---
title: Problemen met Azure Virtual Network NAT-connectiviteit oplossen
titleSuffix: Azure Virtual Network
description: Problemen met Nat voor virtueel netwerk oplossen.
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
ms.date: 03/30/2020
ms.author: allensu
ms.openlocfilehash: c012a8d83761b88cc59b62d11fd3d5542ca7f7a1
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396092"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>Problemen met Azure Virtual Network NAT-connectiviteit oplossen

In dit artikel kunnen beheerders verbindingsproblemen diagnosticeren en oplossen bij het gebruik van Nat in het virtuele netwerk.

## <a name="problems"></a>Problemen

* [SNAT uitputting](#snat-exhaustion)
* [ICMP ping mislukt](#icmp-ping-is-failing)
* [Verbindingsfouten](#connectivity-failures)
* [IPv6 coëxistentie](#ipv6-coexistence)

Volg de stappen in de volgende sectie om deze problemen op te lossen.

## <a name="resolution"></a>Oplossing

### <a name="snat-exhaustion"></a>SNAT uitputting

Eén [NAT-gatewaybron](nat-gateway-resource.md) ondersteunt 64.000 tot 1 miljoen gelijktijdige stromen.  Elk IP-adres biedt 64.000 SNAT-poorten voor de beschikbare voorraad. U maximaal 16 IP-adressen per NAT-gatewaybron gebruiken.  Het SNAT-mechanisme wordt [hier](nat-gateway-resource.md#source-network-address-translation) nader beschreven.

Vaak is de hoofdoorzaak van SNAT-uitputting een anti-patroon voor hoe uitgaande connectiviteit wordt vastgesteld, beheerd of configureerbare timers worden gewijzigd ten opzichte van hun standaardwaarden.  Lees deze sectie aandachtig.

#### <a name="steps"></a>Stappen

1. Controleer of u de standaard inactieve time-out hebt gewijzigd in een waarde die hoger is dan 4 minuten.
2. Onderzoek hoe uw toepassing uitgaande connectiviteit maakt (bijvoorbeeld codecontrole of pakketopname). 
3. Bepaal of deze activiteit wordt verwacht gedrag of dat de toepassing zich misdraagt.  Gebruik [statistieken](nat-metrics.md) in Azure Monitor om uw bevindingen te onderbouwen. Gebruik de categorie 'Mislukt' voor de statistiek SNAT-verbindingen.
4. Evalueren als de juiste patronen worden gevolgd.
5. Evalueer of de uitputting van de SNAT-poort moet worden beperkt met extra IP-adressen die zijn toegewezen aan nat-gatewaybronnen.

#### <a name="design-patterns"></a>Ontwerppatronen

Profiteer altijd van het hergebruik van verbindingen en het bundelen van verbindingen waar mogelijk.  Deze patronen zullen problemen met de uitputting van hulpbronnen voorkomen en leiden tot voorspelbaar gedrag. Primitieven voor deze patronen zijn te vinden in vele ontwikkelingsbibliotheken en frameworks.

_**Oplossing:**_ Gebruik geschikte patronen en aanbevolen procedures

- NAT-gatewaybronnen hebben een standaard idle time-out van TCP van 4 minuten.  Als deze instelling wordt gewijzigd in een hogere waarde, houdt NAT de stromen langer vast en kan [dit onnodige druk op de SNAT-poortvoorraad](nat-gateway-resource.md#timers)veroorzaken.
- Atoomaanvragen (één aanvraag per verbinding) zijn een slechte ontwerpkeuze. Dergelijke anti-patroon beperkt de schaal, vermindert de prestaties en vermindert de betrouwbaarheid. In plaats daarvan u HTTP/S-verbindingen opnieuw gebruiken om het aantal verbindingen en bijbehorende SNAT-poorten te verminderen. De toepassingsschaal wordt verhoogd en de prestaties verbeteren als gevolg van lagere handshakes, overhead- en cryptografische bedrijfskosten bij het gebruik van TLS.
- DNS kan veel afzonderlijke stromen op volume introduceren wanneer de client het resultaat van de DNS-resolvers niet in cache zet. Gebruik caching.
- UDP-stromen (bijvoorbeeld DNS-lookups) wijzen SNAT-poorten toe voor de duur van de niet-actieve time-out. Hoe langer de idle time-out, hoe hoger de druk op SNAT-poorten. Gebruik korte idle time-out (bijvoorbeeld 4 minuten).
- Gebruik verbindingsgroepen om het verbindingsvolume vorm te geven.
- Verlaat nooit in stilte een TCP-stroom en vertrouw op TCP-timers om de stroom op te schonen. Als u TCP de verbinding niet expliciet laat sluiten, blijft de status toegewezen aan tussenliggende systemen en eindpunten en maakt u SNAT-poorten niet beschikbaar voor andere verbindingen. Dit kan leiden tot toepassingsfouten en SNAT-uitputting. 
- Verander tcp close-gerelateerde timerwaarden op OS-niveau niet zonder deskundige kennis van de impact. Hoewel de TCP-stack herstelt, kunnen de prestaties van uw toepassing negatief worden beïnvloed wanneer de eindpunten van een verbinding de verwachtingen niet op elkaar afwerken. De wens om timers te veranderen is meestal een teken van een onderliggend ontwerpprobleem. Bekijk de volgende aanbevelingen.

Vaak kan SNAT-uitputting ook worden versterkt met andere antipatronen in de onderliggende toepassing. Bekijk deze aanvullende patronen en aanbevolen procedures om de schaal en betrouwbaarheid van uw service te verbeteren.

- Ontdek de impact van het verminderen van [idle time-out van TCP](nat-gateway-resource.md#timers) tot lagere waarden, inclusief een standaard time-out van 4 minuten om snat-poortvoorraad eerder vrij te maken.
- Overweeg [asynchrone pollingpatronen](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) voor langlopende bewerkingen om verbindingsbronnen vrij te maken voor andere bewerkingen.
- Langdurige stromen (bijvoorbeeld hergebruikte TCP-verbindingen) moeten TCP-keepalives of keepalives van toepassingslagen gebruiken om tussentijdse systeemtiming te voorkomen. Het verhogen van de niet-actieve time-out is een laatste redmiddel en kan de oorzaak niet oplossen. Een lange time-out kan leiden tot fouten in lage tempo wanneer time-out verloopt en vertraging en onnodige fouten veroorzaken.
- Sierlijke [retry patronen](https://docs.microsoft.com/azure/architecture/patterns/retry) moeten worden gebruikt om agressieve pogingen / uitbarstingen te voorkomen tijdens voorbijgaande mislukking of herstel van het falen.
Het maken van een nieuwe TCP-verbinding voor elke HTTP-bewerking (ook wel "atomaire verbindingen" genoemd) is een anti-patroon.  Atoomverbindingen voorkomen dat uw toepassing goed wordt geschaald en resources worden verwijderd.  Maak altijd meerdere bewerkingen in dezelfde verbinding.  Uw toepassing profiteert van transactiesnelheid en resourcekosten.  Wanneer uw toepassing transportlaagversleuteling (bijvoorbeeld TLS) gebruikt, zijn er aanzienlijke kosten verbonden aan de verwerking van nieuwe verbindingen.  Bekijk [Azure Cloud-ontwerppatronen](https://docs.microsoft.com/azure/architecture/patterns/) voor aanvullende aanbevolen procedures.

#### <a name="additional-possible-mitigations"></a>Aanvullende mogelijke oplossingen

_**Oplossing:**_ Schaal uitgaande connectiviteit als volgt:

| Scenario | Bewijs |Oplossing |
|---|---|---|
| U ondervindt een geschil voor SNAT-poorten en snat-poortuitputting tijdens perioden van hoog gebruik. | De categorie 'Mislukt' [metric](nat-metrics.md) voor SNAT-verbindingen in Azure Monitor toont tijdelijke of aanhoudende fouten in de loop van de tijd en een hoog verbindingsvolume.  | Bepaal of u extra openbare IP-adresbronnen of openbare IP-voorvoegselbronnen toevoegen. Deze toevoeging zal zorgen voor maximaal 16 IP-adressen in totaal naar uw NAT gateway. Deze toevoeging biedt meer voorraad voor beschikbare SNAT-poorten (64.000 per IP-adres) en stelt u in staat om uw scenario verder te schalen.|
| Je hebt al 16 IP-adressen opgegeven en hebt nog steeds last van SNAT-poortuitputting. | Poging om extra IP-adres toe te voegen mislukt. Het totale aantal IP-adressen van openbare IP-adresbronnen of openbare IP-voorvoegselbronnen bedraagt in totaal 16. | Distribueer uw toepassingsomgeving over meerdere subnetten en bied een NAT-gatewaybron voor elk subnet.  Evalueer uw ontwerppatroon(en) opnieuw om te optimaliseren op basis van eerdere [richtlijnen.](#design-patterns) |

>[!NOTE]
>Het is belangrijk om te begrijpen waarom SNAT uitputting optreedt. Zorg ervoor dat u de juiste patronen gebruikt voor schaalbare en betrouwbare scenario's.  Het toevoegen van meer SNAT-poorten aan een scenario zonder de oorzaak van de vraag te begrijpen, zou een laatste redmiddel moeten zijn. Als u niet begrijpt waarom uw scenario druk uitoefent op de snat-poortvoorraad, zal het toevoegen van meer SNAT-poorten aan de voorraad door meer IP-adressen toe te voegen, alleen maar dezelfde uitputtingsfout als de weegschalen van uw toepassing vertragen.  U andere inefficiënties en antipatronen maskeren.

### <a name="icmp-ping-is-failing"></a>ICMP ping mislukt

[Virtual Network NAT](nat-overview.md) ondersteunt IPv4 UDP- en TCP-protocollen. ICMP wordt niet ondersteund en naar verwachting mislukt.  

_**Oplossing:**_ Gebruik in plaats daarvan TCP-verbindingstests (bijvoorbeeld 'TCP-ping') en UDP-specifieke toepassingslaagtests om end-to-end-connectiviteit te valideren.

De volgende tabel kan worden gebruikt een startpunt voor welke tools te gebruiken om tests te starten.

| Besturingssysteem | Generieke TCP-verbindingstest | LAAGtest VOOR TCP-toepassingen | UDP |
|---|---|---|---|
| Linux | nc (generieke verbindingstest) | krul (TCP-toepassingslaagtest) | toepassing specifiek |
| Windows | [PsPing PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | toepassing specifiek |

### <a name="connectivity-failures"></a>Verbindingsfouten

Verbindingsproblemen met [Virtual Network NAT](nat-overview.md) kunnen worden veroorzaakt door verschillende problemen:

* voorbijgaande of aanhoudende [SNAT-uitputting](#snat-exhaustion) van de NAT-gateway;
* tijdelijke fouten in de Azure-infrastructuur, 
* tijdelijke fouten in het pad tussen Azure en de openbare internetbestemming, 
* tijdelijke of aanhoudende storingen op de openbare internetbestemming.

Gebruik tools zoals de volgende voor validatieconnectiviteit. [ICMP-ping wordt niet ondersteund.](#icmp-ping-is-failing)

| Besturingssysteem | Generieke TCP-verbindingstest | LAAGtest VOOR TCP-toepassingen | UDP |
|---|---|---|---|
| Linux | nc (generieke verbindingstest) | krul (TCP-toepassingslaagtest) | toepassing specifiek |
| Windows | [PsPing PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | toepassing specifiek |

#### <a name="snat-exhaustion"></a>SNAT uitputting

Review sectie over [SNAT uitputting](#snat-exhaustion) in dit artikel.

#### <a name="azure-infrastructure"></a>Azure-infrastructuur

Azure bewaakt en exploiteert zijn infrastructuur met grote zorg. Tijdelijke storingen kunnen optreden, er is geen garantie dat transmissies lossless zijn.  Gebruik ontwerppatronen die SYN-doorgifte voor TCP-toepassingen mogelijk maken. Gebruik verbindingstime-outs die groot genoeg zijn om tcp SYN-doorgifte mogelijk te maken om tijdelijke effecten veroorzaakt door een verloren SYN-pakket te verminderen.

_**Oplossing:**_

* Controleer op [SNAT-uitputting.](#snat-exhaustion)
* De configuratieparameter in een TCP-stack die het syn-overdrachtsgedrag regelt, wordt RTO[(Time-Out van overdracht)](https://tools.ietf.org/html/rfc793)genoemd. De RTO-waarde is instelbaar, maar meestal 1 seconde of hoger standaard met exponentiële back-off.  Als de verbindingstime-out van uw toepassing te kort is (bijvoorbeeld 1 seconde), ziet u mogelijk sporadische time-outs van de verbinding.  Verhoog de time-out van de toepassingsverbinding.
* Als u langere, onverwachte time-outs met standaardtoepassingsgedrag observeert, opent u een ondersteuningsaanvraag voor verdere probleemoplossing.

We raden u af om de time-out van de TCP-verbinding kunstmatig te verminderen of de RTO-parameter te tunen.

#### <a name="public-internet-transit"></a>Openbaar internetvervoer

De kans op tijdelijke storingen neemt toe met een langer pad naar de bestemming en meer tussenliggende systemen. De verwachting is dat tijdelijke fouten in frequentie kunnen toenemen ten opzichte van [azure-infrastructuur.](#azure-infrastructure) 

Volg dezelfde richtlijnen als de vorige sectie [Azure-infrastructuur.](#azure-infrastructure)

#### <a name="internet-endpoint"></a>Eindpunt voor internet

De vorige secties zijn van toepassing, samen met het interneteindpunt waarmee communicatie tot stand is gekomen. Andere factoren die van invloed kunnen zijn op het succes van de connectiviteit zijn:

* verkeersbeheer aan bestemmingszijde, inclusief
- API-tariefbeperking opgelegd door de bestemmingszijde
- Volumetric DDoS-mitigatie of het vormgeven van transportlaagverkeer
* firewall of andere componenten op de bestemming 

Meestal pakket vangt bij de bron en de bestemming (indien beschikbaar) zijn vereist om te bepalen wat er gebeurt.

_**Oplossing:**_

* Controleer op [SNAT-uitputting.](#snat-exhaustion) 
* Valideer de connectiviteit met een eindpunt in dezelfde regio of elders voor vergelijking.  
* Als u tests met een hoog volume of transactiesnelheid maakt, onderzoekt u of het verminderen van de snelheid het optreden van fouten vermindert.
* Als het wijzigen van de snelheid van invloed is op de snelheid van fouten, controleert u of API-tarieflimieten of andere beperkingen aan de bestemmingszijde mogelijk zijn bereikt.
* Als uw onderzoek niet overtuigend is, opent u een ondersteuningsaanvraag voor verdere probleemoplossing.

#### <a name="tcp-resets-received"></a>TCP Resets ontvangen

De NAT-gateway genereert TCP-resets op de bron-VM voor verkeer dat niet wordt herkend als in uitvoering.

Een mogelijke reden is dat de TCP-verbinding geen time-out heeft.  U de idle time-out aanpassen van 4 minuten tot maximaal 120 minuten.

TCP-resets worden niet gegenereerd aan de openbare kant van NAT-gatewaybronnen. TCP-resets aan de doelzijde worden gegenereerd door de bron-VM, niet door de NAT-gatewaybron.

_**Oplossing:**_

* Bekijk [aanbevelingen voor ontwerppatronen.](#design-patterns)  
* Open een ondersteuningsaanvraag voor verdere probleemoplossing indien nodig.

### <a name="ipv6-coexistence"></a>IPv6 coëxistentie

[Virtual Network NAT](nat-overview.md) ondersteunt IPv4 UDP- en TCP-protocollen en implementatie op een [subnet met een IPv6-voorvoegsel wordt niet ondersteund.](nat-overview.md#limitations)

_**Oplossing:**_ Implementeer NAT-gateway op een subnet zonder IPv6-voorvoegsel.

U interesse in extra mogelijkheden aangeven via [Virtual Network NAT UserVoice.](https://aka.ms/natuservoice)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [NAT van virtueel netwerk](nat-overview.md)
* Meer informatie over [NAT-gatewaybron](nat-gateway-resource.md)
* Meer informatie over [statistieken en waarschuwingen voor NAT-gatewaybronnen](nat-metrics.md).
* [Vertel ons wat we nu moeten bouwen voor Virtual Network NAT in UserVoice.](https://aka.ms/natuservoice)

