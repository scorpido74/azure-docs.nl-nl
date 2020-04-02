---
title: Wat is Azure Virtual Network NAT?
titlesuffix: Azure Virtual Network
description: Overzicht van nat-functies, resources, architectuur en implementatie van virtueel netwerk. Ontdek hoe Virtual Network NAT werkt en hoe u NAT-gatewaybronnen in de cloud gebruiken.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about Virtual Network NAT, its NAT gateway resources, and what I can use them for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2020
ms.author: allensu
ms.openlocfilehash: 50fc8b9cefe88a80f3f954ce363139b6a4a38589
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548391"
---
# <a name="what-is-virtual-network-nat"></a>Wat is Virtual Network NAT?

Virtual Network NAT (network address translation) vereenvoudigt de internetverbinding met alleen-het-netwerk voor virtuele netwerken. Wanneer geconfigureerd op een subnet, alle uitgaande connectiviteit maakt gebruik van uw opgegeven statische openbare IP-adressen.  Uitgaande connectiviteit is mogelijk zonder load balancer of openbare IP-adressen die direct aan virtuele machines zijn gekoppeld. NAT is volledig beheerd en zeer veerkrachtig.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" width="256" title="Nat virtueel netwerk">
</p>



*Figuur: Virtual Network NAT*

## <a name="static-ip-addresses-for-outbound-only"></a>Statische IP-adressen voor alleen uitgaande

Uitgaande connectiviteit kan worden gedefinieerd voor elk subnet met NAT.  Meerdere subnetten binnen hetzelfde virtuele netwerk kunnen verschillende NAT's hebben. Een subnet wordt geconfigureerd door op te geven welke NAT-gatewaybron moet worden gebruikt. Alle UDP- en TCP-uitgaande stromen van elke virtuele machine-instantie gebruiken NAT. 

NAT is compatibel met standaard SKU public IP-adresbronnen of openbare IP-voorvoegselbronnen of een combinatie van beide.  U een openbaar IP-voorvoegsel rechtstreeks gebruiken of de openbare IP-adressen van het voorvoegsel over meerdere NAT-gatewaybronnen distribueren. NAT verzorgt al het verkeer naar het bereik van IP-adressen van het voorvoegsel.  Elke IP whitelisting van uw implementaties is nu eenvoudig.

Al het uitgaande verkeer voor het subnet wordt automatisch door NAT verwerkt zonder enige klantconfiguratie.  Door de gebruiker gedefinieerde routes zijn niet nodig. NAT heeft voorrang op andere uitgaande scenario's en vervangt de standaardinternetbestemming van een subnet.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>On-demand SNAT met meerdere IP-adressen voor schaal

NAT gebruikt "port network address translation" (PNAT of PAT) en wordt aanbevolen voor de meeste workloads. Dynamische of uiteenlopende workloads kunnen eenvoudig worden opgevangen met on-demand uitgaande stroomtoewijzing. Uitgebreide pre-planning, pre-toewijzing en uiteindelijk overprovisioning van uitgaande middelen wordt vermeden. SNAT-poortbronnen worden gedeeld en beschikbaar voor alle subnetten met behulp van een specifieke NAT-gatewaybron en worden waar nodig geleverd.

Een openbaar IP-adres dat aan NAT is gekoppeld, biedt maximaal 64.000 gelijktijdige stromen voor UDP en TCP. U beginnen met één IP-adres en maximaal 16 openbare IP-adressen opschalen.

NAT maakt het mogelijk stromen te maken van het virtuele netwerk naar het internet. Retourverkeer van het internet is alleen toegestaan als reactie op een actieve stroom.

In tegenstelling tot load balancer uitgaande SNAT, nat heeft geen beperkingen waarop prive IP van een virtuele machine instantie kan uitgaande verbindingen te maken.  Secundaire IP-configuraties kunnen een uitgaande internetverbinding met NAT maken.

## <a name="coexistence-of-inbound-and-outbound"></a>Coëxistentie van in- en uitgaande

NAT is compatibel met de volgende standaard SKU-resources:

- Load balancer
- Openbaar IP-adres
- Voorvoegsel voor het openbare IP-adres

Wanneer deze bronnen samen met NAT worden gebruikt, bieden deze bronnen inkomende internetverbinding met uw subnet(s). NAT biedt alle uitgaande internetverbinding van uw subnet(s).

NAT en compatibele Standaard SKU-functies zijn zich bewust van de richting waarin de stroom is gestart. Inkomende en uitgaande scenario's kunnen naast elkaar bestaan. Deze scenario's ontvangen de juiste netwerkadresvertalingen omdat deze functies op de hoogte zijn van de stroomrichting. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" width="512" title="Nat-stroomrichting voor virtueel netwerk">
</p>

*Figuur: Nat-stroomrichting voor virtueel netwerk*

## <a name="fully-managed-highly-resilient"></a>Volledig beheerd, zeer veerkrachtig

NAT is vanaf het begin volledig uitgeschaald. Er is geen opvoerende of scale-out operatie vereist.  Azure beheert de werking van NAT voor u.  NAT heeft altijd meerdere foutdomeinen en kan meerdere storingen voorkomen zonder serviceuitval.

## <a name="tcp-reset-for-unrecognized-flows"></a>TCP-reset voor niet-herkende stromen

De privékant van NAT stuurt TCP Reset-pakketten voor pogingen om te communiceren via een TCP-verbinding die niet bestaat. Een voorbeeld hiervan zijn verbindingen die een niet-actieve time-out hebben bereikt. In het volgende ontvangen pakket wordt een TCP Reset teruggegeven aan het privé-IP-adres om de verbinding te seinen en te forceren.

De openbare kant van NAT genereert geen TCP Reset-pakketten of ander verkeer.  Alleen verkeer dat door het virtuele netwerk van de klant wordt geproduceerd, wordt uitgestoten.

## <a name="configurable-tcp-idle-timeout"></a>Configureerbare TCP-idle time-out

Een standaard TCP idle time-out van 4 minuten wordt gebruikt en kan worden verhoogd tot maximaal 120 minuten. Elke activiteit op een stroom kan ook de niet-actieve timer resetten, inclusief TCP-keepalives.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Regionale isolatie of zone-isolatie met beschikbaarheidszones

NAT is standaard regionaal. Bij het maken [van scenario's voor beschikbaarheidszones](../availability-zones/az-overview.md) kan NAT worden geïsoleerd in een specifieke zone (zonale implementatie).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" width="512" title="Nat van virtueel netwerk met beschikbaarheidszones">
</p>

*Figuur: Virtual Network NAT met beschikbaarheidszones*

## <a name="multi-dimensional-metrics-for-observability"></a>Multidimensionale statistieken voor waarneembaarheid

U de werking van uw NAT controleren via multidimensionale statistieken die worden weergegeven in Azure Monitor. Deze statistieken kunnen worden gebruikt om het gebruik en voor het oplossen van problemen te observeren.  NAT-gatewaybronnen geven de volgende statistieken bloot:
- Bytes
- Pakketten
- Gedropte pakketten
- Totale SNAT-verbindingen
- SNAT-verbindingsstatusovergangen per interval.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>SLA

Bij algemene beschikbaarheid is NAT-gegevenspad ten minste 99,9% beschikbaar.


## <a name="pricing"></a>Prijzen

NAT-gateway wordt gefactureerd met twee afzonderlijke meters:

| Meter | Tarief |
| --- | --- |
| Resource-uren | $0.045/uur |
| Gegevens verwerkt | $ 0,045/GB |

Resourceuren zijn goed voor de duur waarin een NAT-gatewaybron bestaat.
Gegevens verwerkte accounts voor al het verkeer dat wordt verwerkt door een NAT-gatewaybron.

## <a name="availability"></a>Beschikbaarheid

Nat van het virtuele netwerk en de NAT-gatewaybron zijn beschikbaar in alle azure public [cloud-regio's.](https://azure.microsoft.com/global-infrastructure/regions/)

## <a name="support"></a>Ondersteuning

NAT wordt ondersteund via normale ondersteuningskanalen.

## <a name="feedback"></a>Feedback

We willen weten hoe we de service kunnen verbeteren. Voorstellen en stemmen over wat we moeten bouwen volgende op [UserVoice voor NAT](https://aka.ms/natuservoice).


## <a name="limitations"></a>Beperkingen

* NAT is compatibel met standaard SKU public IP, public IP-voorvoegsel en load balancer-resources. Basisresources, zoals basisloadbalancer en alle producten die daaruit zijn afgeleid, zijn niet compatibel met NAT.  Basisresources moeten worden geplaatst op een subnet dat niet is geconfigureerd met NAT.
* IPv4-adresfamilie wordt ondersteund.  NAT heeft geen interactie met IPv6-adresfamilie.  NAT kan niet worden geïmplementeerd op een subnet met een IPv6-voorvoegsel.
* NSG-stroomlogboekregistratie wordt niet ondersteund bij het gebruik van NAT.
* NAT kan niet meerdere virtuele netwerken omvatten.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [NAT-gatewaybron](./nat-gateway-resource.md).
* [Vertel ons wat we nu moeten bouwen voor Virtual Network NAT in UserVoice.](https://aka.ms/natuservoice)

