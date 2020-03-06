---
title: Wat is Azure Virtual Network NAT?
description: Overzicht van Virtual Network NAT-functies,-bronnen,-architectuur en-implementatie. Meer informatie over de werking van Virtual Network NAT en het gebruik van NAT-gateway bronnen in de Cloud.
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
ms.date: 03/05/2020
ms.author: allensu
ms.openlocfilehash: 205826a6ad952383582f5a8086cbd8b85dbc3794
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359257"
---
# <a name="what-is-virtual-network-nat-public-preview"></a>Wat is Virtual Network NAT (open bare preview)?

Virtual Network NAT (Network Address Translation) vereenvoudigt alleen uitgaande internet connectiviteit voor virtuele netwerken. Wanneer de configuratie op een subnet is geconfigureerd, gebruikt alle uitgaande verbindingen uw opgegeven statische open bare IP-adressen.  Uitgaande verbindingen zijn mogelijk zonder load balancer of open bare IP-adressen die rechtstreeks zijn gekoppeld aan virtuele machines. NAT is volledig beheerd en zeer flexibel.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" width="256" title="Virtual Network NAT">
</p>



*Afbeelding: Virtual Network NAT*


>[!NOTE] 
>Virtual Network NAT is op dit moment beschikbaar als open bare preview. Het is momenteel alleen beschikbaar in een beperkt aantal [regio's](#region-availability). Deze preview is beschikbaar zonder service level agreement en wordt niet aanbevolen voor productie werkbelastingen. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="static-ip-addresses-for-outbound-only"></a>Statische IP-adressen voor alleen-uitgaand verkeer

Voor elk subnet met NAT kan uitgaande connectiviteit worden gedefinieerd.  Meerdere subnetten binnen hetzelfde virtuele netwerk kunnen verschillende Nat's hebben. Een subnet wordt geconfigureerd door de [NAT-gateway resource](./nat-gateway-resource.md) op te geven die moet worden gebruikt. Alle uitgaande UDP-en TCP-stromen van alle exemplaren van een virtuele machine worden NAT gebruikt. 

NAT is compatibel met Standard SKU [open bare IP-adres bronnen](./virtual-network-ip-addresses-overview-arm.md#standard) of [open bare IP-prefix bronnen](./public-ip-address-prefix.md) of een combi natie van beide.  U kunt rechtstreeks een openbaar IP-voor voegsel gebruiken of de open bare IP-adressen van het voor voegsel verdelen over meerdere NAT gateway-resources. NAT zal al het verkeer opschonen naar het bereik van de IP-adressen van het voor voegsel.  Elk IP-white list van uw implementaties is nu eenvoudig.

Al het uitgaande verkeer voor het subnet wordt automatisch door NAT verwerkt zonder dat er een klant configuratie is.  Door de gebruiker gedefinieerde routes zijn niet nodig. NAT heeft voor rang op andere [uitgaande scenario's](../load-balancer/load-balancer-outbound-connections.md) en vervangt de standaard Internet bestemming van een subnet.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>SNAT op aanvraag met meerdere IP-adressen voor schalen

NAT gebruikt ' poort Network Address Translation ' (PNAT of PAT) en wordt aanbevolen voor de meeste werk belastingen. Dynamische of uiteenlopende werk belastingen kunnen eenvoudig worden afgestemd op een uitgaande stroom toewijzing op aanvraag. Een uitgebreide vooraf-planning, vooraf-toewijzing en uiteindelijk overmatiging van uitgaande bronnen wordt voor komen. De SNAT-poort resources worden gedeeld en beschikbaar in alle subnetten met behulp van een specifieke NAT-gateway resource en worden weer gegeven wanneer dat nodig is.

Een openbaar IP-adres dat is gekoppeld aan NAT biedt Maxi maal 64.000 gelijktijdige stromen voor UDP en TCP. U kunt beginnen met één IP-adres en Maxi maal 16 open bare IP-adressen schalen.

NAT maakt het mogelijk om stromen te maken van het virtuele netwerk naar het internet. Het retour verkeer van het internet is alleen toegestaan als reactie op een actieve stroom.

In tegens telling tot load balancer uitgaande SNAT heeft NAT geen beperkingen voor het privé-IP-adres van een exemplaar van een virtuele machine, waarmee uitgaande verbindingen kunnen worden gemaakt.  Secundaire IP-configuraties kunnen uitgaande Internet verbinding maken met NAT.

## <a name="coexistence-of-inbound-and-outbound"></a>Samen werking van inkomend en uitgaand

NAT is compatibel met de volgende standaard SKU-resources:

- [Load balancer](../load-balancer/load-balancer-overview.md)
- [Openbaar IP-adres](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [Openbaar IP-voor voegsel](../virtual-network/public-ip-address-prefix.md)

Bij gebruik in combi natie met NAT bieden deze bronnen inkomende Internet connectiviteit voor uw subnet (s). NAT biedt alle uitgaande internet connectiviteit van uw subnet ('s).

NAT en compatibele standaard-SKU-functies zijn op de hoogte van de richting waarin de stroom is gestart. De scenario's voor inkomend en uitgaand verkeer kunnen naast elkaar bestaan. In deze scenario's worden de juiste vertalingen van het netwerk adres weer gegeven, omdat deze functies op de hoogte zijn van de richting van de stroom. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" width="512" title="Richting van Virtual Network NAT-stroom">
</p>

*Afbeelding: de richting van de NAT-stroom Virtual Network*

## <a name="fully-managed-highly-resilient"></a>Volledig beheerd, zeer flexibel

NAT is volledig uitgebreid van het begin. Er is geen afronding of uitbreilijke bewerking vereist.  Azure beheert de werking van NAT voor u.  NAT heeft altijd meerdere fout domeinen en kan meerdere storingen ondervinden zonder service onderbreking.

## <a name="tcp-reset-for-unrecognized-flows"></a>TCP Reset voor niet-herkende stromen

De persoonlijke kant van NAT verzendt TCP-Reset pakketten voor pogingen om te communiceren op een TCP-verbinding die niet bestaat. Een voor beeld hiervan is verbindingen die een time-out voor inactiviteit hebben bereikt. Het volgende ontvangen pakket retourneert een TCP Reset naar het privé-IP-adres om het sluiten van de verbinding te Signa leren en af te dwingen.

De open bare zijde van NAT genereert geen pakketten voor TCP-Reset of andere verkeer.  Alleen verkeer dat door het virtuele netwerk van de klant wordt geproduceerd, wordt verzonden.

## <a name="configurable-idle-timeout"></a>Configureer bare time-out voor inactiviteit

Een standaard time-out voor inactiviteit van vier minuten wordt gebruikt en kan tot 120 minuten worden verhoogd. Elke activiteit in een stroom kan ook de niet-actieve timer opnieuw instellen, inclusief TCP-keepalives.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Regionale of zone-isolatie met beschikbaarheids zones

NAT is standaard regionaal. Wanneer u scenario's voor [beschikbaarheids zones](../availability-zones/az-overview.md) maakt, kan nat worden geïsoleerd in een specifieke zone (zonegebonden-implementatie).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" width="512" title="Virtual Network NAT met beschikbaarheids zones">
</p>

*Afbeelding: Virtual Network NAT met beschikbaarheids zones*

## <a name="multi-dimensional-metrics-for-observability"></a>Multi-dimensionale metrische gegevens voor de naleving

U kunt de werking van uw NAT bewaken door middel van multidimensionale metrische gegevens die beschikbaar zijn in Azure Monitor. Deze metrische gegevens kunnen worden gebruikt om het gebruik te observeren en voor het oplossen van problemen.  De resources van de NAT-gateway bieden de volgende metrische gegevens:
- Bytes
- Pakket
- Verwijderde pakketten
- Totaal SNAT-verbindingen
- Overdrachten van SNAT-verbindings status per interval.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>SLA

Bij algemene Beschik baarheid is het NAT-gegevenspad ten minste 99,9% beschikbaar.

## <a name = "region-availability"></a>Beschik baarheid van regio

NAT is momenteel beschikbaar in deze regio's:

- Europa - west
- Japan - oost
- US - oost 2
- US - west
- US - west 2
- US - west-centraal

## <a name = "enable-preview"></a>Deelname aan open bare preview

Abonnementen moeten worden geregistreerd om deel te nemen aan de open bare preview.  Deelname vereist een proces met twee stappen en instructies worden hieronder vermeld voor Azure CLI en Azure PowerShell.  Het kan enkele minuten duren voordat de activering is voltooid.

### <a name="azure-cli"></a>Azure CLI

1. abonnement registreren voor open bare preview

    ```azurecli-interactive
      az feature register --namespace Microsoft.Network --name AllowNatGateway
    ```

2. registratie activeren

    ```azurecli-interactive
      az provider register --namespace Microsoft.Network
    ```

### <a name="azure-powershell"></a>Azure PowerShell

1. abonnement registreren voor open bare preview

    ```azurepowershell-interactive
      Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowNatGateway
    ```

2. registratie activeren

    ```azurepowershell-interactive
      Register-AzResourceProvider -ProviderNamespace Microsoft.Network
    ```

## <a name="pricing"></a>Prijzen

De NAT-gateway wordt gefactureerd met twee afzonderlijke meters:

| Meter | Tarief |
| --- | --- |
| Resource-uren | $0.045/uur |
| Verwerkte gegevens | $0.045/GB |

Resource-uren accounts voor de duur waarin een NAT-gateway bron bestaat.
Gegevens verwerkte accounts voor al het verkeer dat is verwerkt door een NAT-gateway resource.

Tijdens de open bare preview worden prijzen gedisconteerd op 50%.

## <a name="support"></a>Ondersteuning

NAT wordt ondersteund via normale ondersteunings kanalen.

## <a name="feedback"></a>Feedback

We willen weten hoe we de service kunnen verbeteren. Deel uw [feedback over de open bare preview-versie](https://aka.ms/natfeedback) met ons.  En u kunt Voorst Ellen en stemmen op wat we moeten bouwen op [UserVoice voor NAT](https://aka.ms/natuservoice).

## <a name="limitations"></a>Beperkingen

* NAT is compatibel met een standaard-SKU openbaar IP-adres, een openbaar IP-voor voegsel en load balancer-resources.   Basis bronnen (bijvoorbeeld basis load balancer) en alle producten die hiervan zijn afgeleid, zijn niet compatibel met NAT.  Basis bronnen moeten worden geplaatst op een subnet dat niet is geconfigureerd met NAT.
* De IPv4-adres familie wordt ondersteund.  NAT communiceert niet met een IPv6-adres groep.  NAT kan niet worden geïmplementeerd op een subnet met IPv6-voor voegsel.
* Logboek registratie van de NSG-stroom wordt niet ondersteund bij gebruik van NAT.
* NAT kan niet meerdere virtuele netwerken omvatten.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [NAT gateway-resource](./nat-gateway-resource.md).
* [Vertel ons wat u nu kunt bouwen voor Virtual Network nat in UserVoice](https://aka.ms/natuservoice).
* [Feedback geven over de open bare preview](https://aka.ms/natfeedback).
