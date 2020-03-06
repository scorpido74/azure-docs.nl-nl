---
title: Virtuele netwerken ontwerpen met NAT-gateway bronnen
titleSuffix: Azure Virtual Network NAT
description: Meer informatie over het ontwerpen van virtuele netwerken met NAT-gateway bronnen.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about how to design virtual networks with NAT gateway resources.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: d920bde856521f1e662536c1187881e143612039
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359097"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources-public-preview"></a>Virtuele netwerken ontwerpen met NAT-gateway bronnen (open bare preview)

NAT-gateway bronnen maken deel uit van [Virtual Network NAT](nat-overview.md) en bieden een uitgaande Internet verbinding voor een of meer subnetten van een virtueel netwerk. In het subnet van het virtuele netwerk staat welke NAT-gateway wordt gebruikt. NAT biedt bron Network Address Translation (SNAT) voor een subnet.  NAT gateway-resources geven aan welke statische IP-adressen virtuele machines gebruiken bij het maken van uitgaande stromen. Statische IP-adressen zijn afkomstig van open bare IP-adres bronnen, open bare IP-voorvoegsel bronnen of beide. Een NAT-gateway bron kan Maxi maal 16 vaste IP-adressen van beide gebruiken.


<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="NAT Virtual Network voor uitgaand verkeer naar Internet">
</p>

*Afbeelding: Virtual Network NAT voor uitgaand verkeer naar Internet*


>[!NOTE] 
>Virtual Network NAT is op dit moment beschikbaar als open bare preview. Het is momenteel alleen beschikbaar in een beperkt aantal [regio's](nat-overview.md#region-availability). Deze preview is beschikbaar zonder service level agreement en wordt niet aanbevolen voor productie werkbelastingen. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="how-to-deploy-nat"></a>NAT implementeren

Het configureren en gebruiken van NAT-gateway is opzettelijk eenvoudig gemaakt:  

NAT-gateway resource:
- Een regionale of zonegebonden (zone-geïsoleerde) NAT-gateway resource maken
- IP-adressen toewijzen,
- Time-out voor inactiviteit wijzigen (optioneel).

Virtueel netwerk:
- Het subnet van het virtuele netwerk configureren voor het gebruik van een NAT-gateway.

Door de gebruiker gedefinieerde routes zijn niet nodig.

## <a name="resource"></a>Resource

De resource is zo ontworpen dat u dit eenvoudig kunt zien in het volgende Azure Resource Manager voor beeld in een sjabloon achtige indeling.  Deze sjabloon achtige indeling wordt hier weer gegeven om de concepten en de structuur te illustreren.  Wijzig het voor beeld voor uw behoeften.  Dit document is niet bedoeld als zelf studie.

In het volgende diagram ziet u de Beschrijf bare verwijzingen tussen de verschillende Azure Resource Manager resources.  De pijl geeft de richting van de verwijzing aan, afkomstig van de locatie waar deze kan worden geschreven. Beoordelen 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" width="256" title="Virtual Network NAT-object model">
</p>

*Afbeelding: Virtual Network NAT-object model*

NAT wordt aanbevolen voor de meeste werk belastingen, tenzij u een specifieke afhankelijkheid hebt van [Load Balancer uitgaande verbindingen op basis](../load-balancer/load-balancer-outbound-connections.md)van een groep.  

U kunt migreren van standaard load balancer scenario's, met inbegrip van [Uitgaande regels](../load-balancer/load-balancer-outbound-rules-overview.md), naar de NAT-gateway. Als u wilt migreren, verplaatst u de open bare IP-en open bare IP-adres voorvoegsel resources van load balancer-front-end naar de NAT-gateway. Nieuwe IP-adressen voor NAT-gateway zijn niet vereist. Het standaard open bare IP-adres en het voor voegsel kunnen opnieuw worden gebruikt, zolang het totaal niet groter is dan 16 IP-adressen. Plan voor migratie met onderbreking van de service tijdens de overgang.  U kunt de onderbreking minimaliseren door het proces te automatiseren. Test eerst de migratie in een faserings omgeving.  Tijdens de overgang worden de inkomende stromen niet beïnvloed.

In het volgende voor beeld wordt een NAT-gateway resource met de naam _myNATGateway_ gemaakt in regio _Oost VS 2, AZ 1_ met een time-out van _4 minuten_ . De gegeven uitgaande IP-adressen zijn:
- Een set open bare IP-adres bronnen _myIP1_ en _myIP2_ en 
- Een set open bare IP-voor voegsel bronnen _myPrefix1_ en _myPrefix2_. 

Het totale aantal IP-adressen dat door alle vier IP-adres bronnen wordt verschaft, mag niet meer zijn dan 16 IP-adressen. Een wille keurig aantal IP-adressen tussen 1 en 16 is toegestaan.

```json
{
"name": "myNATGateway",
   "type": "Microsoft.Network/natGateways",
   "apiVersion": "2018-11-01",
   "location": "East US 2",
   "sku": { "name": "Standard" },
   "zones": [ "1" ],
   "properties": {
      "idleTimeoutInMinutes": 4, 
      "publicIPPrefixes": [
         {
            "id": "ref to myPrefix1"
         },
         {
            "id": "ref to myPrefix2"
         }
      ],
      "publicIPAddresses": [
         {
            "id": "ref to myIP1"
         },
         {
            "id": "ref to myIP2"
         }
      ]
   }
}
```

Wanneer de NAT gateway-bron is gemaakt, kan deze worden gebruikt voor een of meer subnetten van een virtueel netwerk. Geef op welke subnetten deze NAT-gateway Resource gebruiken. Een NAT-gateway kan niet meer dan één virtueel netwerk omvatten. Het is niet nodig om dezelfde NAT-gateway toe te wijzen aan alle subnetten van een virtueel netwerk. Afzonderlijke subnetten kunnen worden geconfigureerd met verschillende NAT gateway-resources.

Scenario's waarin geen beschikbaarheids zones worden gebruikt, zijn regionaal (geen zone opgegeven). Als u beschikbaarheids zones gebruikt, kunt u een zone opgeven om NAT te isoleren voor een specifieke zone. Zone-redundantie wordt niet ondersteund. Controleer de NAT- [beschikbaarheids zones](#availability-zones).


```json
{
   "name": "myVNet",
   "apiVersion": "2018-11-01",
   "type": "Microsoft.Network/virtualNetworks",
   "location": "myRegion", 
   "properties": {
      "addressSpace": {
          "addressPrefixes": [
           "192.168.0.0/16"
          ]
      },
      "subnets": [
         {
            "name": "mySubnet1",
            "properties": {
               "addressPrefix": "192.168.0.0/24",
               "natGateway": {
                  "id": "ref to myNATGateway"
               }
            }
         } 
      ]
   }
}
```
NAT-gateways worden gedefinieerd met een eigenschap in een subnet binnen een virtueel netwerk. Stromen die zijn gemaakt door virtuele machines op subnet _mySubnet1_ van virtuele netwerk _myVNet_ , gebruiken de NAT-gateway. Alle uitgaande verbindingen gebruiken de IP-adressen die zijn gekoppeld aan _myNatGateway_ als bron-IP-adres.


## <a name="design-guidance"></a>Ontwerp richtlijnen

Lees deze sectie om vertrouwd te raken met overwegingen voor het ontwerpen van virtuele netwerken met NAT.  

1. [Kosten optimalisatie](#cost-optimization)
1. [Samen werking van inkomend en uitgaand](#coexistence-of-inbound-and-outbound)
2. [Basis resources beheren](#managing-basic-resources)
3. [Beschikbaarheidszones](#availability-zones)

### <a name="cost-optimization"></a>Kostenoptimalisatie

[Service-eind punten](virtual-network-service-endpoints-overview.md) en [privé koppeling](../private-link/private-link-overview.md) zijn twee opties waarmee u rekening moet houden voor de optimalisatie van de kosten, waarbij NAT niet nodig is.  Verkeer dat naar service-eind punten of privé koppeling wordt geleid, wordt niet verwerkt door de NAT van het virtuele netwerk.  

Service-eind punten koppelen Azure-service resources aan uw virtuele netwerk en beheren de toegang tot uw Azure-service resources. Als u bijvoorbeeld toegang hebt tot Azure Storage, gebruikt u een service-eind punt voor opslag om te voor komen dat er NAT-kosten worden verwerkt. Service-eind punten zijn gratis.

Met persoonlijke koppeling wordt de Azure PaaS-service (of andere services die worden gehost met een privé-koppeling) weer gegeven als een persoonlijk eind punt in een virtueel netwerk.  Een persoonlijke koppeling wordt gefactureerd op basis van de duur en de verwerkte gegevens.

Bepaal of een van beide benaderingen goed past voor uw scenario en gebruik waar nodig.

### <a name="coexistence-of-inbound-and-outbound"></a>Samen werking van inkomend en uitgaand

De NAT-gateway is compatibel met:

 - Standaard load balancer
 - Standaard openbaar IP
 - Standaard openbaar IP-voor voegsel

Wanneer u een nieuwe implementatie ontwikkelt, begint u met standaard-Sku's.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="NAT Virtual Network voor uitgaand verkeer naar Internet">
</p>

*Afbeelding: Virtual Network NAT voor uitgaand verkeer naar Internet*

Het alleen-uitgaand scenario voor Internet van de NAT-gateway kan worden uitgebreid met inkomend verkeer van Internet functionaliteit. Elke resource is op de hoogte van de richting waarin een stroom afkomstig is. In een subnet met een NAT-gateway worden alle uitgaande internet scenario's vervangen door de NAT-gateway. Inkomende van Internet scenario's worden door de betreffende resource verschaft.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT en VM met openbaar IP-adres op exemplaar niveau

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" width="300" title="Virtual Network NAT en VM met een openbaar IP-adres op exemplaar niveau">
</p>

*Afbeelding: Virtual Network NAT en VM met een openbaar IP-adres op exemplaar niveau*

| Richting | Resource |
|:---:|:---:|
| Inkomend | VM met openbaar IP-adres op exemplaar niveau |
| Uitgaand | NAT-gateway |

De virtuele machine gebruikt de NAT-gateway voor uitgaand verkeer.  Dit is niet van invloed op inkomend verkeer.

#### <a name="nat-and-vm-with-public-load-balancer"></a>NAT en VM met open bare Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" width="350" title="Virtual Network NAT en VM met open bare Load Balancer">
</p>

*Afbeelding: NAT en VM Virtual Network met open bare Load Balancer*

| Richting | Resource |
|:---:|:---:|
| Inkomend | open bare Load Balancer |
| Uitgaand | NAT-gateway |

Elke uitgaande configuratie van een taakverdelings regel of uitgaande regels wordt vervangen door de NAT-gateway.  Dit is niet van invloed op inkomend verkeer.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT en VM met openbaar IP-adres en open bare Load Balancer op exemplaar niveau

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" width="425" title="Virtual Network NAT en VM met openbaar IP-adres en open bare Load Balancer op exemplaar niveau">
</p>

*Afbeelding: Virtual Network NAT en VM met openbaar IP-adres en open bare Load Balancer op exemplaar niveau*

| Richting | Resource |
|:---:|:---:|
| Inkomend | VM met openbaar IP-adres en open bare Load Balancer op exemplaar niveau |
| Uitgaand | NAT-gateway |

Elke uitgaande configuratie van een taakverdelings regel of uitgaande regels wordt vervangen door de NAT-gateway.  De virtuele machine gebruikt ook de NAT-gateway voor uitgaand verkeer.  Dit is niet van invloed op inkomend verkeer.

### <a name="managing-basic-resources"></a>Basis resources beheren

De standaard load balancer, het open bare IP-adres en het open bare IP-voor voegsel zijn compatibel met de NAT-gateway. NAT-gateways kunnen worden gebruikt binnen het bereik van een subnet. De basis-SKU van deze services moet worden geïmplementeerd op een subnet zonder een NAT-gateway. Met deze schei ding kunnen zowel SKU-varianten naast elkaar bestaan in hetzelfde virtuele netwerk.

NAT-gateways hebben voor rang op uitgaande scenario's van het subnet. Basis load balancer of een openbaar IP-adres (en een ingebouwde beheerde service) kan niet worden aangepast met de juiste vertalingen. De NAT-gateway beheert de controle over het uitgaande Internet verkeer in een subnet. Binnenkomend verkeer naar Basic load balancer en openbaar IP-adres is niet beschikbaar. Binnenkomend verkeer naar een basis load balancer, of een openbaar IP-adres dat op een VM is geconfigureerd, is niet beschikbaar.

### <a name="availability-zones"></a>Beschikbaarheidszones

Zelfs zonder beschikbaarheids zones is NAT robuust en kunnen er meerdere storingen in het infrastructuur onderdeel optreden. Wanneer beschikbaarheids zones deel uitmaken van uw scenario, moet u NAT configureren voor een specifieke zone.  De besturings vlak bewerkingen en het gegevens vlak zijn beperkt tot de opgegeven zone. Er is een fout opgetreden in een andere zone dan waar uw scenario voor komt, wordt naar verwachting zonder invloed op NAT. Uitgaand verkeer van virtuele machines in dezelfde zone kan niet worden uitgevoerd vanwege isolatie van de zone.

<p align="center">
  <img src="media/nat-overview/az-directions.svg" width="425" title="Virtual Network NAT met beschikbaarheids zones">
</p>

*Afbeelding: Virtual Network NAT met beschikbaarheids zones*

Een zone-geïsoleerde NAT-gateway vereist dat IP-adressen overeenkomen met de zone van de NAT-gateway. NAT gateway-resources met IP-adressen uit een andere zone of zonder een zone worden niet ondersteund.

Virtuele netwerken en subnetten zijn regionaal en niet zonegebonden uitgelijnd. Een virtuele machine moet zich in dezelfde zone bevinden als de NAT-gateway voor een zonegebonden-belofte van uitgaande verbindingen. Zone-isolatie wordt gemaakt door het maken van een zonegebonden per beschikbaarheids zone. Er bestaat geen zonegebonden-belofte wanneer u zones van een zonegebonden NAT-gateway kruist of gebruikmaakt van een regionale NAT-gateway met zonegebonden-Vm's.

Wanneer u schaal sets voor virtuele machines implementeert voor gebruik met NAT, implementeert u een zonegebonden-schaalset op een eigen subnet en koppelt u de overeenkomende zone NAT-gateway aan dat subnet. Als u schaal sets voor zone-spanning gebruikt (een schaalset in twee of meer zones), biedt NAT geen zonegebonden-belofte.  NAT biedt geen ondersteuning voor zone-redundantie.  Alleen regionale of zone-isolatie wordt ondersteund.

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" width="425" title="zone-omspannen Virtual Network NAT">
</p>

*Afbeelding: zone-omspannen Virtual Network NAT*

De eigenschap zones is niet onveranderbaar.  Implementeer de NAT-gateway resource opnieuw met de beoogde regionale of zone voorkeur.

>[!NOTE] 
>IP-adressen op zichzelf zijn niet zone-redundant als er geen zone is opgegeven.  De front-end van een [Standard Load Balancer is zone-redundant](../load-balancer/load-balancer-standard-availability-zones.md#frontend) als er geen IP-adres in een specifieke zone wordt gemaakt.  Dit is niet van toepassing op NAT.  Alleen regionale of zone-isolatie wordt ondersteund.

## <a name="source-network-address-translation"></a>Adres omzetting bron netwerk

Bron Network Address Translation (SNAT) herschrijft de bron van een stroom naar afkomstig van een ander IP-adres.  NAT gateway-resources gebruiken een variant van de SNAT die meestal wordt aangeduid met poort adres omzetting (PAT). PAT herschrijft het bron adres en de bron poort. Met SNAT is er geen vaste relatie tussen het aantal privé adressen en de vertaalde open bare adressen.  

### <a name="fundamentals"></a>Basisprincipes

Laten we eens kijken naar een voor beeld van vier stromen om het basis concept uit te leggen.  De NAT-gateway maakt gebruik van de resource 65.52.0.2 van het open bare IP-adres.

| Stroom | Bron-tuple | Doel-tuple |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Deze stromen kunnen er als volgt uitzien nadat er een PAT heeft plaatsgevonden:

| Stroom | Bron-tuple | SNAT'ed-bron-tuple | Doel-tuple | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.2:234 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.2:235 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.2:236 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:237 | 65.52.0.2:80 |

De doel bron van de stroom wordt weer gegeven als 65.52.0.2 (SNAT-bron-tuple) met de toegewezen poort.  PAT zoals weer gegeven in de voor gaande tabel wordt ook poort masker SNAT genoemd.  Meerdere persoonlijke bronnen worden achter een IP-adres en poort bemaskerd.

Maak geen afhankelijk van de specifieke manier waarop bron poorten worden toegewezen.  De voor gaande is een illustratie van alleen het fundamentele concept.

De door NAT verschafte SNAT wijkt af van [Load Balancer](../load-balancer/load-balancer-outbound-connections.md) in verschillende aspecten.

### <a name="on-demand"></a>Op aanvraag

NAT biedt SNAT-poorten op aanvraag voor nieuwe uitgaande verkeers stromen. Alle beschik bare SNAT-poorten in de inventaris worden gebruikt door een virtuele machine op subnetten die zijn geconfigureerd met NAT. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" width="550" title="Virtual Network NAT op aanvraag uitgaande SNAT">
</p>

*Afbeelding: Virtual Network NAT op aanvraag uitgaande SNAT*

Elke IP-configuratie van een virtuele machine kan indien nodig uitgaande stromen op aanvraag maken.  De planning van vóór de toewijzing per exemplaar, inclusief per exemplaar slechts een overinrichting, is niet vereist.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" width="550" title="Verschillen in de afzuig scenario's">
</p>

*Afbeelding: verschillen in de afzuig scenario's*

Zodra een SNAT-poort is vrijgegeven, is deze beschikbaar voor gebruik door elke virtuele machine op subnetten die zijn geconfigureerd met NAT.  Bij toewijzing op aanvraag kunnen dynamische en uiteenlopende-workloads op subnetten gebruikmaken van SNAT-poorten die nodig zijn.  Zolang er een SNAT-poort voorraad beschikbaar is, slagen de SNAT-stromen. In plaats daarvan profiteren de hot spots van een SNAT-poort van de grotere inventarisatie. De SNAT-poorten worden niet gebruikt voor virtuele machines die niet actief zijn.

### <a name="scaling"></a>Schalen

NAT vereist voldoende SNAT-poort inventaris voor het volledige uitgaande scenario. Het schalen van NAT is hoofd zakelijk een functie voor het beheren van de gedeelde, beschik bare SNAT-poort inventaris. Er moet voldoende inventarisatie bestaan om de piek uitgaande stroom te verhelpen voor alle subnetten die zijn gekoppeld aan een NAT-gateway resource.

Met SNAT worden meerdere privé adressen toegewezen aan één openbaar adres en kunnen meerdere open bare Ip's worden geschaald.

Een NAT-gateway bron maakt gebruik van 64.000-poorten (SNAT-poorten) van een openbaar IP-adres.  Deze SNAT-poorten worden de beschik bare inventarisatie voor de toewijzing van privé naar open bare stroom. En het toevoegen van meer open bare IP-adressen verhoogt de beschik bare inventarisatie-SNAT-poorten. NAT-gateway bronnen kunnen tot 16 IP-adressen en 1 miljoen SNAT-poorten schalen.  TCP en UDP zijn afzonderlijke SNAT-poort inventarisatie en zijn niet gerelateerd.

NAT-gateway bronnen gebruiken een opportunistisch gebruik van bron poorten. Voor schaal doeleinden moet u aannemen dat elke stroom een nieuwe SNAT-poort vereist en het totale aantal beschik bare IP-adressen voor uitgaand verkeer schalen.

### <a name="protocols"></a>Protocollen

NAT gateway-resources communiceren met IP-en IP-transport headers van UDP-en TCP-stromen en zijn neutraal naar nettoladingen van toepassingsmappartities.  Andere IP-protocollen worden niet ondersteund.

### <a name="timers"></a>Timers

Time-out voor inactiviteit kan worden aangepast van 4 minuten (standaard) tot 120 minuten (2 uur) voor alle stromen.  Daarnaast kunt u de niet-actieve timer opnieuw instellen met verkeer op de stroom.  Een aanbevolen patroon voor het vernieuwen van langlopende inactieve verbindingen en de detectie van endpointies van de levens duur van TCP-keepalives.  TCP-keepalives worden weer gegeven als dubbele bevestigingen voor de eind punten, zijn weinig overhead en zijn onzichtbaar voor de toepassingslaag.

De volgende timers worden gebruikt voor de SNAT-poort versie:

| Timer | Waarde |
|---|---|
| TCP-FIN | 60 seconden |
| EERSTE TCP | 10 seconden |
| TCP half open | 30 seconden |

Een SNAT-poort kan na vijf seconden opnieuw worden gebruikt voor hetzelfde doel-IP-adres en dezelfde doel poort.

>[!NOTE] 
>Deze timer instellingen zijn onderhevig aan wijzigingen. De waarden worden opgegeven om te helpen bij het oplossen van problemen en u moet op dit moment geen afhankelijkheid nemen van specifieke timers.

## <a name="limitations"></a>Beperkingen

- NAT is compatibel met een standaard-SKU openbaar IP-adres, een openbaar IP-voor voegsel en load balancer-resources.   Basis bronnen (bijvoorbeeld basis load balancer) en alle producten die hiervan zijn afgeleid, zijn niet compatibel met NAT.  Basis bronnen moeten worden geplaatst op een subnet dat niet is geconfigureerd met NAT.
- De IPv4-adres familie wordt ondersteund.  NAT communiceert niet met een IPv6-adres groep.  NAT kan niet worden geïmplementeerd op een subnet met IPv6-voor voegsel.
- Logboek registratie van de NSG-stroom wordt niet ondersteund bij gebruik van NAT.
- NAT kan niet meerdere virtuele netwerken omvatten.

## <a name="preview-participation"></a>Preview-deelname

Volg [de instructies om uw abonnement in te scha kelen](nat-overview.md#public-preview-participation).

## <a name="feedback"></a>Feedback

We willen weten hoe we de service kunnen verbeteren. Deel uw [feedback over de open bare preview-versie](https://aka.ms/natfeedback) met ons.  En u kunt Voorst Ellen en stemmen op wat we moeten bouwen op [UserVoice voor NAT](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over NAT voor het [virtuele netwerk](nat-overview.md).
* Meer informatie over [metrische gegevens en waarschuwingen voor NAT-gateway bronnen](nat-metrics.md).
* Meer informatie over het [oplossen van problemen met NAT-gateway bronnen](troubleshoot-nat.md).
* [Vertel ons wat u nu kunt bouwen voor Virtual Network nat in UserVoice](https://aka.ms/natuservoice).
* [Feedback geven over de open bare preview](https://aka.ms/natfeedback).
* Zelf studie voor het valideren van de NAT-gateway
  - [Azure cli](tutorial-create-validate-nat-gateway-cli.md),
  - [Power shell](tutorial-create-validate-nat-gateway-cli.md),
  - [Portal](tutorial-create-validate-nat-gateway-cli.md)
* Snelstartgids voor het implementeren van een NAT-gateway resource
  - [Azure cli](./quickstart-create-nat-gateway-cli.md),
  - [Power shell](./quickstart-create-nat-gateway-powershell.md),
  - [Portal](./quickstart-create-nat-gateway-portal.md).
* Meer informatie over de resource-API voor NAT-gateway
  - [rest API](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways),
  - [Azure cli](https://docs.microsoft.com/cli/azure/network/nat/gateway?view=azure-cli-latest),
  - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway).
* Meer informatie over [beschikbaarheids zones](../availability-zones/az-overview.md).
* Meer informatie over [standaard Load Balancer](../load-balancer/load-balancer-standard-overview.md).
* Meer informatie over [beschikbaarheids zones en standaard Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md).


