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
ms.date: 03/02/2020
ms.author: allensu
ms.openlocfilehash: 185556e3045cb1a879bc256f4f2932549de71cd8
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274940"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Problemen met Azure Virtual Network NAT-connectiviteit oplossen

Dit artikel helpt beheerders bij het vaststellen en oplossen van verbindings problemen bij het gebruik van Virtual Network NAT.

>[!NOTE] 
>Virtual Network NAT is op dit moment beschikbaar als open bare preview. Het is momenteel alleen beschikbaar in een beperkt aantal [regio's](nat-overview.md#region-availability). Deze preview is beschikbaar zonder service level agreement en wordt niet aanbevolen voor productie werkbelastingen. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="problems"></a>Problemen

- [Afvoer van SNAT](#snat-exhaustion).
- [ICMP-ping is mislukt](#icmp-ping-is-failing).

Volg de stappen in de volgende sectie om deze problemen op te lossen.

## <a name="resolution"></a>Oplossing

### <a name="snat-exhaustion"></a>SNAT-uitputting

Eén [NAT-gateway resource](nat-gateway-resource.md) ondersteunt van 64.000 tot 1.000.000 gelijktijdige stromen.  Elk IP-adres levert een 64.000 SNAT-poort aan de beschik bare inventaris. U kunt Maxi maal 16 IP-adressen per NAT-gateway Resource gebruiken.  Het SNAT-mechanisme wordt [hier](nat-gateway-resource.md#source-network-address-translation) uitgebreid beschreven.

#### <a name="steps"></a>Stappen:

1. Onderzoek hoe uw toepassing uitgaande connectiviteit maakt (bijvoorbeeld code controle of pakket opname). 
2. Bepaal of deze activiteit verwacht gedrag is of of de toepassing niet goed werkt.  Gebruik metrische gegevens in Azure Monitor om uw bevindingen te staven.
3. Evalueren of de juiste patronen worden gevolgd.
4. Evalueren of de SNAT-poort uitputting moet worden verminderd met extra IP-adressen die zijn toegewezen aan de NAT gateway-resource.

#### <a name="design-pattern"></a>Ontwerp patroon:

Maak waar mogelijk altijd gebruik van verbindings hergebruik en verbindings Pools.  Dit patroon voor komt problemen met bron uitputting en resulteert in voorspel bare werking. Primitieven voor deze patronen vindt u in veel ontwikkel bibliotheken en frameworks.
- Denk aan [asynchrone polling-patronen](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) voor langlopende bewerkingen om verbindings bronnen vrij te maken voor andere bewerkingen.
- Lange levens stromen (bijvoorbeeld hergebruikte TCP-verbindingen) moeten TCP-keepalives of keepalives van de toepassingslaag gebruiken om time-out van tussenliggende systemen te voor komen.
- Geslaagde [patronen voor opnieuw proberen](https://docs.microsoft.com/azure/architecture/patterns/retry) moeten worden gebruikt om agressieve pogingen/bursts te voor komen tijdens tijdelijke storingen of herstel na storingen.
Het maken van een nieuwe TCP-verbinding voor elke HTTP-bewerking (ook wel ' atomische verbindingen ' genoemd) is een anti-patroon.  Met atomische verbindingen wordt voor komen dat uw toepassing goed kan worden geschaald en verspilt resources.  Meerdere bewerkingen in een pijp lijn altijd in dezelfde verbinding.  Uw toepassing zal profiteren van de transactie snelheid en resource kosten.  Als uw toepassing gebruikmaakt van trans port Layer encryption (bijvoorbeeld TLS), zijn er aanzienlijke kosten verbonden aan de verwerking van nieuwe verbindingen.  Bekijk de [ontwerp patronen voor Azure-Clouds](https://docs.microsoft.com/azure/architecture/patterns/) voor aanvullende best practice patronen.

#### <a name="mitigations"></a>Oplossingen

U kunt uitgaande verbindingen als volgt schalen:

| Scenario | Oplossing |
|---|---|
| U ondervindt conflicten voor SNAT-poorten en SNAT-poort uitputting tijdens peri Oden met een hoog gebruik. | Bepaal of u meer open bare IP-adres bronnen of open bare IP-prefix bronnen kunt toevoegen. Met deze toevoeging kunnen Maxi maal 16 IP-adressen in totaal naar uw NAT-gateway worden toegevoegd. Deze toevoeging biedt meer inventarisatie voor de beschik bare SNAT-poorten (64.000 per IP-adres) en stelt u in staat om uw scenario verder te schalen.|
| U hebt al 16 IP-adressen opgegeven en er is nog steeds een SNAT-poort uitgeput. | Distribueer uw toepassings omgeving over meerdere subnetten en geef een NAT-gateway bron op voor elk subnet. |

>[!NOTE]
>Het is belang rijk om te begrijpen waarom de uitputting van de SNAT optreedt. Zorg ervoor dat u de juiste patronen gebruikt voor schaal bare en betrouw bare scenario's.  Het toevoegen van meer SNAT-poorten aan een scenario zonder dat de oorzaak van de vraag een laatste redmiddel moet zijn. Als u geen inzicht krijgt in waarom uw scenario druk op de SNAT-poort inventariseert, kunt u meer SNAT-poorten toevoegen aan de inventaris door meer IP-adressen toe te voegen, waardoor er slechts één uitputtings fout optreedt wanneer uw toepassing wordt geschaald.  U kunt andere inefficiënties en anti-patronen maskeren.

### <a name="icmp-ping-is-failing"></a>ICMP-ping is mislukt

[Virtual Network NAT](nat-overview.md) ondersteunt IPv4 UDP-en TCP-protocollen. ICMP wordt niet ondersteund en wordt naar verwachting mislukt.  Gebruik in plaats daarvan TCP-verbindings tests (bijvoorbeeld ' TCP ping ') en UDP-specifieke Application Layer-tests om end-to-end-verbindingen te valideren.

De volgende tabel kan worden gebruikt als uitgangs punt voor de hulpprogram ma's die u wilt gebruiken om tests te starten.

| Besturingssysteem | Algemene TCP-verbindings test | Test van de TCP-toepassingslaag | UDP |
|---|---|---|---|
| Linux | NC (algemene verbindings test) | krul (test voor TCP-toepassingslaag) | toepassingsspecifieke |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | Power shell [invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | toepassingsspecifieke |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Virtual Network NAT](nat-overview.md)
- Meer informatie over de [NAT gateway-resource](nat-gateway-resource.md)
