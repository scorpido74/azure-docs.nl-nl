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
ms.date: 04/27/2020
ms.author: allensu
ms.openlocfilehash: 6bb53539c105cda99c842b6b0fa236f0e18a85ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82182477"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>Virtuele netwerken ontwerpen met NAT-gateway bronnen

NAT-gateway bronnen maken deel uit van [Virtual Network NAT](nat-overview.md) en bieden een uitgaande Internet verbinding voor een of meer subnetten van een virtueel netwerk. In het subnet van het virtuele netwerk staat welke NAT-gateway wordt gebruikt. NAT biedt bron Network Address Translation (SNAT) voor een subnet.  NAT gateway-resources geven aan welke statische IP-adressen virtuele machines gebruiken bij het maken van uitgaande stromen. Statische IP-adressen zijn afkomstig van open bare IP-adres bronnen, open bare IP-voorvoegsel bronnen of beide. Als er een open bare IP-prefix bron wordt gebruikt, worden alle IP-adressen van de volledige open bare IP-voor voegsel resource verbruikt door een NAT-gateway resource. Een NAT-gateway resource kan in totaal Maxi maal 16 vaste IP-adressen gebruiken.


<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="NAT Virtual Network voor uitgaand verkeer naar Internet">
</p>

*Afbeelding: Virtual Network NAT voor uitgaand verkeer naar Internet*

## <a name="how-to-deploy-nat"></a>NAT implementeren

Het configureren en gebruiken van NAT-gateway is opzettelijk eenvoudig gemaakt:  

NAT-gateway resource:
- Een regionale of zonegebonden (zone-geïsoleerde) NAT-gateway resource maken
- IP-adressen toewijzen,
- Wijzig indien nodig TCP-time-out voor inactiviteit (optioneel).  Bekijk [timers](#timers) <ins>voordat</ins> u de standaard instelling wijzigt.

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

U kunt migreren van standaard load balancer scenario's, met inbegrip van [Uitgaande regels](../load-balancer/load-balancer-outbound-rules-overview.md), naar de NAT-gateway. Als u wilt migreren, verplaatst u de open bare IP-en open bare IP-adres voorvoegsel resources van load balancer-front-end naar de NAT-gateway. Nieuwe IP-adressen voor NAT-gateway zijn niet vereist. Standaard open bare IP-adres bronnen en open bare IP-prefix bronnen kunnen opnieuw worden gebruikt zolang het totaal niet groter is dan 16 IP-adressen. Plan voor migratie met onderbreking van de service tijdens de overgang.  U kunt de onderbreking minimaliseren door het proces te automatiseren. Test eerst de migratie in een faserings omgeving.  Tijdens de overgang worden de inkomende stromen niet beïnvloed.


Het volgende voor beeld is een fragment van een Azure Resource Manager sjabloon.  Met deze sjabloon worden verschillende bronnen geïmplementeerd, waaronder een NAT-gateway.  In dit voor beeld bevat de sjabloon de volgende para meters:

- **natgatewayname** : de naam van de NAT-gateway.
- **locatie** : Azure-regio waar de resource zich bevindt.
- **publicipname** : de naam van het uitgaande open bare IP-adres dat is gekoppeld aan de NAT-gateway.
- **vnetname** : de naam van het virtuele netwerk.
- **subnetnaam** -naam van het subnet dat is gekoppeld aan de NAT-gateway.

Het totale aantal IP-adressen dat door alle IP-adressen en voorvoegsel bronnen wordt verschaft, mag niet groter zijn dan 16 IP-adressen. Een wille keurig aantal IP-adressen tussen 1 en 16 is toegestaan.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="81-96":::

Wanneer de NAT gateway-bron is gemaakt, kan deze worden gebruikt voor een of meer subnetten van een virtueel netwerk. Geef op welke subnetten deze NAT-gateway Resource gebruiken. Een NAT-gateway kan niet meer dan één virtueel netwerk omvatten. Het is niet nodig om dezelfde NAT-gateway toe te wijzen aan alle subnetten van een virtueel netwerk. Afzonderlijke subnetten kunnen worden geconfigureerd met verschillende NAT gateway-resources.

Scenario's waarin geen beschikbaarheids zones worden gebruikt, zijn regionaal (geen zone opgegeven). Als u beschikbaarheids zones gebruikt, kunt u een zone opgeven om NAT te isoleren voor een specifieke zone. Zone-redundantie wordt niet ondersteund. Controleer de NAT- [beschikbaarheids zones](#availability-zones).

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="1-146" highlight="81-96":::

NAT-gateways worden gedefinieerd met een eigenschap in een subnet binnen een virtueel netwerk. Stromen die zijn gemaakt door virtuele machines in het subnet **subnet** van het virtuele netwerk **vnetname** , maken gebruik van de NAT-gateway. Alle uitgaande verbindingen gebruiken de IP-adressen die zijn gekoppeld aan **natgatewayname** als bron-IP-adres.

Voor meer informatie over de Azure Resource Manager-sjabloon die in dit voor beeld wordt gebruikt, raadpleegt u:

- [Snelstartgids: een NAT-gateway maken-Resource Manager-sjabloon](quickstart-create-nat-gateway-template.md)
- [Virtual Network NAT](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm/)

## <a name="design-guidance"></a>Ontwerp richtlijnen

Lees deze sectie om vertrouwd te raken met overwegingen voor het ontwerpen van virtuele netwerken met NAT.  

1. [Kosten optimalisatie](#cost-optimization)
1. [Samen werking van inkomend en uitgaand](#coexistence-of-inbound-and-outbound)
2. [Basis resources beheren](#managing-basic-resources)
3. [Beschikbaarheidszones](#availability-zones)

### <a name="cost-optimization"></a>Kostenoptimalisatie

[Service-eind punten](virtual-network-service-endpoints-overview.md) en [privé koppeling](../private-link/private-link-overview.md) zijn opties waarmee u rekening moet houden voor het optimaliseren van de kosten. NAT is niet nodig voor deze services. Verkeer dat is bestemd voor service-eind punten of privé koppeling, wordt niet verwerkt door de NAT van het virtuele netwerk.  

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

#### <a name="zone-isolation-with-zonal-stacks"></a>Zone isolatie met zonegebonden-stacks

<p align="center">
  <img src="media/nat-overview/az-directions.svg" width="425" title="Virtual Network NAT met zone isolatie, meerdere maken "zonal stacks"">
</p>

*Afbeelding: Virtual Network NAT met zone isolatie, meerdere ' zonegebonden-stacks maken '*

Zelfs zonder beschikbaarheids zones is NAT robuust en kunnen er meerdere storingen in het infrastructuur onderdeel optreden.  Beschikbaarheids zones zijn gebaseerd op deze tolerantie met isolatie scenario's voor de zone voor NAT.

Virtuele netwerken en hun subnetten zijn regionale constructies.  Subnetten zijn niet beperkt tot een zone.

Er bestaat een zonegebonden-belofte voor zone isolatie wanneer een exemplaar van een virtuele machine met een NAT-gateway bron zich in dezelfde zone bevindt als de NAT-gateway bron en de bijbehorende open bare IP-adressen. Het patroon dat u wilt gebruiken voor zone isolatie is het maken van een ' zonegebonden-stack ' per beschikbaarheids zone.  Deze ' zonegebonden-stack ' bestaat uit virtuele-machine-instanties, NAT-gateway bronnen, openbaar IP-adres en/of voorvoegsel bronnen in een subnet waarvoor alleen dezelfde zone wordt gebruikt.   De bewerkingen en het gegevens vlak van het besturings vlak worden vervolgens uitgelijnd met en beperkt tot de opgegeven zone. 

Er is een fout opgetreden in een andere zone dan waar uw scenario voor komt, wordt naar verwachting zonder invloed op NAT. Uitgaand verkeer van virtuele machines in dezelfde zone kan niet worden uitgevoerd vanwege isolatie van de zone.  

#### <a name="integrating-inbound-endpoints"></a>Binnenkomende eind punten integreren

Als voor uw scenario inkomende eind punten zijn vereist, hebt u twee opties:

| Optie | Patroon | Voorbeeld | Pro | CON |
|---|---|---|---|---|
| i | De inkomende eind punten **Uitlijnen** met de respectieve **zonegebonden-stacks** die u maakt voor uitgaand verkeer. | Maak een standaard load balancer met zonegebonden front-end. | Hetzelfde status model en dezelfde fout modus voor inkomend en uitgaand. Eenvoudiger te kunnen worden gebruikt. | Afzonderlijke IP-adressen per zone moeten mogelijk worden gemaskeerd met een algemene DNS-naam. |
| (2) | De zonegebonden-stacks **bedekken** met een inkomend eind punt voor **meerdere zones** . | Maak een standaard load balancer met een zone-redundante front-end. | Eén IP-adres voor inkomend eind punt. | Verschillende status modellen en storings modi voor inkomend en uitgaand verkeer.  Ingewik kelder. |

>[!NOTE]
> Een zone-geïsoleerde NAT-gateway vereist dat IP-adressen overeenkomen met de zone van de NAT-gateway. NAT gateway-resources met IP-adressen uit een andere zone of zonder een zone zijn niet toegestaan.

#### <a name="cross-zone-outbound-scenarios-not-supported"></a>Uitgaande scenario's met meerdere zones worden niet ondersteund

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" width="425" title="Virtual Network NAT is niet compatibel met het zone-bereik subnet">
</p>

*Afbeelding: Virtual Network NAT niet compatibel met het zone-bereik subnet*

U kunt geen zonegebonden-belofte bevoorraden met NAT-gateway bronnen wanneer virtuele machine-instanties worden geïmplementeerd in meerdere zones binnen hetzelfde subnet.   En zelfs als er meerdere zonegebonden NAT-gateways zijn gekoppeld aan een subnet, weet het exemplaar van de virtuele machine niet welke NAT-gateway resource kan selecteren.

Er bestaat een zonegebonden Promise does't wanneer de zone van een exemplaar van een virtuele machine en de zones van een zonegebonden NAT-gateway niet zijn uitgelijnd of b) een regionale NAT-gateway resource wordt gebruikt met zonegebonden exemplaren van virtuele machines.

Terwijl het scenario wordt weer gegeven, is het status model en de fout modus niet gedefinieerd vanuit het oogpunt van de beschikbaarheids zone. Overweeg om in plaats daarvan te gaan met zonegebonden-stacks of alle regio's.

>[!NOTE]
>De eigenschap zones van een NAT-gateway resource is niet onveranderbaar.  Implementeer de NAT-gateway resource opnieuw met de beoogde regionale of zone voorkeur.

>[!NOTE] 
>IP-adressen op zichzelf zijn niet zone-redundant als er geen zone is opgegeven.  De front-end van een [Standard Load Balancer is zone-redundant](../load-balancer/load-balancer-standard-availability-zones.md#frontend) als er geen IP-adres in een specifieke zone wordt gemaakt.  Dit is niet van toepassing op NAT.  Alleen regionale of zone-isolatie wordt ondersteund.

## <a name="performance"></a>Prestaties

Elke gateway bron van NAT kan tot 50 Gbps aan door Voer bieden. U kunt uw implementaties splitsen in meerdere subnetten en elk subnet of groepen subnets toewijzen een NAT-gateway om uit te schalen.

Elke NAT-gateway kan 64.000 verbindingen per toegewezen uitgaand IP-adres ondersteunen.  Raadpleeg de volgende sectie over de bron netwerk adres omzetting (SNAT) voor meer informatie en het [artikel over probleem oplossing](https://docs.microsoft.com/azure/virtual-network/troubleshoot-nat) voor specifieke richt lijnen voor probleem oplossing.

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

Het schalen van NAT is hoofd zakelijk een functie voor het beheren van de gedeelde, beschik bare SNAT-poort inventaris. NAT heeft voldoende SNAT-poort inventarisatie nodig voor verwachte piek uitgaande stromen voor alle subnetten die zijn gekoppeld aan een NAT-gateway resource.  U kunt open bare IP-adres bronnen, open bare IP-prefix bronnen of beide gebruiken om een SNAT-poort inventaris te maken.  

>[!NOTE]
>Als u een resource voor een openbaar IP-adres toewijst, wordt het volledige open bare IP-voor voegsel gebruikt.  U kunt geen open bare IP-prefix bron toewijzen en vervolgens afzonderlijke IP-adressen uitdelen om ze toe te wijzen aan andere resources.  Als u afzonderlijke IP-adressen van een openbaar IP-voor voegsel aan meerdere resources wilt toewijzen, moet u afzonderlijke open bare IP-adressen maken op basis van de open bare IP-prefix resource en deze indien nodig toewijzen in plaats van de open bare IP-prefix resource zelf.

Met SNAT worden privé adressen toegewezen aan een of meer open bare IP-adressen, waarbij het bron adres en de bron poort worden herschreven in de processen. Een NAT-gateway bron maakt gebruik van 64.000-poorten (SNAT-poorten) per geconfigureerd openbaar IP-adres voor deze vertaling. NAT-gateway bronnen kunnen tot 16 IP-adressen en 1 miljoen SNAT-poorten schalen. Als er een open bare IP-prefix bron wordt opgegeven, levert elk IP-adres in het voor voegsel een SNAT-poort inventaris. En het toevoegen van meer open bare IP-adressen verhoogt de beschik bare inventarisatie-SNAT-poorten. TCP en UDP zijn afzonderlijke SNAT-poort inventarisatie en zijn niet gerelateerd.

NAT-gateway bronnen gebruiken een opportunistisch gebruik van bron poorten. Voor schaal doeleinden moet u aannemen dat elke stroom een nieuwe SNAT-poort vereist en het totale aantal beschik bare IP-adressen voor uitgaand verkeer schalen.

### <a name="protocols"></a>Protocollen

NAT gateway-resources communiceren met IP-en IP-transport headers van UDP-en TCP-stromen en zijn neutraal naar nettoladingen van toepassingsmappartities.  Andere IP-protocollen worden niet ondersteund.

### <a name="timers"></a>Timers

>[!IMPORTANT]
>Lange timer voor inactiviteit kan onnodig groter worden dan de kans op SNAT-uitputting. Hoe langer de timer die u opgeeft, hoe lang de NAT-poorten langer duren totdat de time-out voor inactiviteit uiteindelijk afloopt. Als er een time-out optreedt voor uw stromen, zullen ze uiteindelijk toch mislukken en onnodig een SNAT-poort inventariseren.  Stromen die gedurende 2 uur mislukken, zouden in de standaard 4 minuten zijn mislukt. Het verg Roten van de time-out voor inactiviteit is een optie voor laatste redmiddel die spaarzaam moet worden gebruikt. Als een stroom nooit inactief gaat, wordt deze niet beïnvloed door de niet-actieve timer.

De time-out voor inactiviteit van TCP kan worden aangepast van 4 minuten (standaard) tot 120 minuten (2 uur) voor alle stromen.  Daarnaast kunt u de niet-actieve timer opnieuw instellen met verkeer op de stroom.  Een aanbevolen patroon voor het vernieuwen van langlopende inactieve verbindingen en de detectie van endpointies van de levens duur van TCP-keepalives.  TCP-keepalives worden weer gegeven als dubbele bevestigingen voor de eind punten, zijn weinig overhead en zijn onzichtbaar voor de toepassingslaag.

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
- De IPv4-adres familie wordt ondersteund.  NAT communiceert niet met een IPv6-adres groep.  NAT kan niet worden geïmplementeerd op een subnet met een IPv6-voor voegsel.
- Logboek registratie van de NSG-stroom wordt niet ondersteund bij gebruik van NAT.
- NAT kan niet meerdere virtuele netwerken omvatten.


## <a name="feedback"></a>Feedback

We willen weten hoe we de service kunnen verbeteren. Ontbreekt er een mogelijkheid? Doe het volgende voor wat we moeten bouwen op [UserVoice voor NAT](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over NAT voor het [virtuele netwerk](nat-overview.md).
* Meer informatie over [metrische gegevens en waarschuwingen voor NAT-gateway bronnen](nat-metrics.md).
* Meer informatie over het [oplossen van problemen met NAT-gateway bronnen](troubleshoot-nat.md).
* Zelf studie voor het valideren van de NAT-gateway
  - [Azure-CLI](tutorial-create-validate-nat-gateway-cli.md)
  - [PowerShell](tutorial-create-validate-nat-gateway-powershell.md)
  - [Portal](tutorial-create-validate-nat-gateway-portal.md)
* Snelstartgids voor het implementeren van een NAT-gateway resource
  - [Azure-CLI](./quickstart-create-nat-gateway-cli.md)
  - [PowerShell](./quickstart-create-nat-gateway-powershell.md)
  - [Portal](./quickstart-create-nat-gateway-portal.md)
  - [Sjabloon](./quickstart-create-nat-gateway-template.md)
* Meer informatie over de resource-API voor NAT-gateway
  - [REST-API](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways)
  - [Azure-CLI](https://docs.microsoft.com/cli/azure/network/nat/gateway?view=azure-cli-latest)
  - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway)
* Meer informatie over [beschikbaarheids zones](../availability-zones/az-overview.md).
* Meer informatie over [standaard Load Balancer](../load-balancer/load-balancer-standard-overview.md).
* Meer informatie over [beschikbaarheids zones en standaard Load Balancer](../load-balancer/load-balancer-standard-availability-zones.md).
* [Vertel ons wat u nu kunt bouwen voor Virtual Network nat in UserVoice](https://aka.ms/natuservoice).


