---
title: Virtuele netwerken ontwerpen met NAT-gatewaybronnen
titleSuffix: Azure Virtual Network NAT
description: Meer informatie over het ontwerpen van virtuele netwerken met NAT-gatewaybronnen.
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
ms.date: 04/09/2020
ms.author: allensu
ms.openlocfilehash: 4095b0b48e86b0aafcc86d74ca1fa25bacddf0ec
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011715"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>Virtuele netwerken ontwerpen met NAT-gatewaybronnen

NAT-gatewaybronnen maken deel uit van [Virtual Network NAT](nat-overview.md) en bieden uitgaande internetverbinding voor een of meer subnetten van een virtueel netwerk. In het subnet van het virtuele netwerk staat welke NAT-gateway wordt gebruikt. NAT biedt bronnetwerkadresvertaling (SNAT) voor een subnet.  NAT-gatewaybronnen geven aan welke statische IP-adressen virtuele machines gebruiken bij het maken van uitgaande stromen. Statische IP-adressen zijn afkomstig van openbare IP-adresbronnen, openbare IP-voorvoegselbronnen of beide. Een NAT-gatewaybron kan maximaal 16 statische IP-adressen van beide gebruiken.


<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtual Network NAT voor uitgaande naar internet">
</p>

*Figuur: Virtual Network NAT voor uitgaande naar internet*

## <a name="how-to-deploy-nat"></a>NAT implementeren

Het configureren en gebruiken van NAT-gateway wordt opzettelijk eenvoudig gemaakt:  

NAT-gatewaybron:
- Regionale of zonale NAT-gatewaybron (zone-geïsoleerd) maken,
- IP-adressen toewijzen,
- Wijzig indien nodig een idle time-out van TCP (optioneel).  Controleer [timers](#timers) <ins>voordat</ins> u de standaardinstelling wijzigt.

Virtueel netwerk:
- Configureer virtueel netwerksubnet om een NAT-gateway te gebruiken.

Door de gebruiker gedefinieerde routes zijn niet nodig.

## <a name="resource"></a>Resource

De bron is ontworpen om eenvoudig te zijn zoals u zien in het volgende voorbeeld van Azure Resource Manager in een sjabloonachtige indeling.  Deze sjabloon-achtige notatie wordt hier getoond om de concepten en structuur te illustreren.  Wijzig het voorbeeld voor uw behoeften.  Dit document is niet bedoeld als een zelfstudie.

In het volgende diagram worden de schrijfbare verwijzingen weergegeven tussen de verschillende Azure Resource Manager-resources.  De pijl geeft de richting van de verwijzing aan, afkomstig van waar deze kan worden geschreven. Beoordelen 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" width="256" title="NAT-objectmodel voor virtueel netwerk">
</p>

*Figuur: NAT-objectmodel voor virtueel netwerk*

NAT wordt aanbevolen voor de meeste workloads, tenzij u een specifieke afhankelijkheid hebt van uitgaande connectiviteit op basis van load balancer op [basis van een groep.](../load-balancer/load-balancer-outbound-connections.md)  

U migreren van standaard scenario's voor load balancer, inclusief [uitgaande regels,](../load-balancer/load-balancer-outbound-rules-overview.md)naar NAT-gateway. Als u wilt migreren, verplaatst u de openbare ip- en openbare ip-voorvoegselbronnen van de frontends van load balancer naar NAT-gateway. Nieuwe IP-adressen voor NAT-gateway zijn niet vereist. Standaard openbaar IP- en voorvoegsel kan opnieuw worden gebruikt zolang het totaal niet hoger is dan 16 IP-adressen. Plan voor migratie met serviceonderbreking in het achterhoofd tijdens de overgang.  U de onderbreking minimaliseren door het proces te automatiseren. Test de migratie eerst in een faseringsomgeving.  Tijdens de overgang worden inkomende stromen niet beïnvloed.

Het volgende voorbeeld is een fragment van een Azure Resource Manager-sjabloon.  Met deze sjabloon worden verschillende bronnen geïmplementeerd, waaronder een NAT-gateway.  De sjabloon heeft in dit voorbeeld de volgende parameters:

- **natgatewayname** - Naam van de NAT-gateway.
- **locatie** - Azure-gebied waar de bron zich bevindt.
- **publicipname** - Naam van het uitgaande openbare IP dat is gekoppeld aan de NAT-gateway.
- **vnetname** - Naam van het virtuele netwerk.
- **subnetnaam** - Naam van het subnet dat is gekoppeld aan de NAT-gateway.

Het totale aantal IP-adressen dat door alle IP-adres- en voorvoegselbronnen wordt verstrekt, mag niet meer bedragen dan 16 IP-adressen in totaal. Een willekeurig aantal IP-adressen tussen 1 en 16 is toegestaan.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="81-96":::

Wanneer de NAT-gatewaybron is gemaakt, kan deze worden gebruikt op een of meer subnetten van een virtueel netwerk. Geef op welke subnetten deze NAT-gatewaybron gebruiken. Een NAT-gateway kan niet meer dan één virtueel netwerk omvatten. Het is niet vereist om dezelfde NAT-gateway toe te wijzen aan alle subnetten van een virtueel netwerk. Afzonderlijke subnetten kunnen worden geconfigureerd met verschillende NAT-gatewaybronnen.

Scenario's die geen gebruik maken van beschikbaarheidszones zijn regionaal (geen zone opgegeven). Als u beschikbaarheidszones gebruikt, u een zone opgeven om NAT te isoleren naar een specifieke zone. Zone-redundantie wordt niet ondersteund. [Nat-beschikbaarheidszones controleren](#availability-zones).

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="1-146" highlight="81-96":::

NAT-gateways worden gedefinieerd met een eigenschap op een subnet binnen een virtueel netwerk. Stromen die door virtuele machines op **subnetsubnetnaam** van virtuele **netwerkvnetname** worden gemaakt, gebruiken de NAT-gateway. Alle uitgaande connectiviteit gebruikt de IP-adressen die zijn gekoppeld aan **natgatewaynaam** als bron-IP-adres.

Zie voor meer informatie over de sjabloon Azure Resource Manager die in dit voorbeeld wordt gebruikt:

- [Snelstart: een NAT-gateway maken - sjabloon Resourcemanager](quickstart-create-nat-gateway-template.md)
- [Nat virtueel netwerk](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm/)

## <a name="design-guidance"></a>Ontwerprichtlijnen

Bekijk deze sectie om vertrouwd te raken met overwegingen voor het ontwerpen van virtuele netwerken met NAT.  

1. [Kostenoptimalisatie](#cost-optimization)
1. [Coëxistentie van in- en uitgaande](#coexistence-of-inbound-and-outbound)
2. [Basisbronnen beheren](#managing-basic-resources)
3. [Beschikbaarheidszones](#availability-zones)

### <a name="cost-optimization"></a>Kostenoptimalisatie

[Serviceeindpunten](virtual-network-service-endpoints-overview.md) en [privékoppeling](../private-link/private-link-overview.md) zijn opties om rekening mee te houden voor het optimaliseren van de kosten. NAT is niet nodig voor deze diensten. Verkeer dat naar serviceeindpunten of privékoppeling wordt geleid, wordt niet verwerkt door de NAT van het virtuele netwerk.  

Serviceeindpunten koppelen Azure-serviceresources aan uw virtuele netwerk en beheren de toegang tot uw Azure-servicebronnen. Wanneer u bijvoorbeeld toegang krijgt tot Azure-opslag, gebruikt u een serviceeindpunt voor opslag om te voorkomen dat gegevens verwerkt NAT-kosten worden verwerkt. Service eindpunten zijn gratis.

Privékoppeling stelt Azure PaaS-service (of andere services die worden gehost met een privékoppeling) bloot als een privéeindpunt binnen een virtueel netwerk.  Privékoppeling wordt gefactureerd op basis van duur en verwerkte gegevens.

Evalueren of een van deze of beide benaderingen zijn een goede pasvorm voor uw scenario en gebruik als dat nodig is.

### <a name="coexistence-of-inbound-and-outbound"></a>Coëxistentie van in- en uitgaande

NAT-gateway is compatibel met:

 - Standaard load balancer
 - Standaard openbaar IP
 - Standaard openbaar IP-voorvoegsel

Bij het ontwikkelen van een nieuwe implementatie, beginnen met standaard SKU's.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtual Network NAT voor uitgaande naar internet">
</p>

*Figuur: Virtual Network NAT voor uitgaande naar internet*

Het internet-uitgaande scenario dat door NAT-gateway wordt geboden, kan worden uitgebreid met binnenkomend van internetfunctionaliteit. Elke resource is zich bewust van de richting waarin een stroom is ontstaan. Op een subnet met een NAT-gateway worden alle uitgaande naar internetscenario's vervangen door de NAT-gateway. Binnenkomend van internetscenario's worden geleverd door de desbetreffende resource.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT en VM met openbaar IP op instantieniveau

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" width="300" title="Nat en VM op virtueel netwerk met openbaar IP op instantieniveau">
</p>

*Figuur: Nat en VM op virtueel netwerk met openbaar IP op instantieniveau*

| Richting | Resource |
|:---:|:---:|
| Inkomend | VM met openbaar IP op instantieniveau |
| Uitgaand | NAT-gateway |

VM gebruikt NAT-gateway voor uitgaande.  Inkomende is ontstaan wordt niet beïnvloed.

#### <a name="nat-and-vm-with-public-load-balancer"></a>NAT en VM met openbare Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" width="350" title="Nat en VM voor virtueel netwerk met openbare load balancer">
</p>

*Figuur: Virtual Network NAT en VM met openbare Load Balancer*

| Richting | Resource |
|:---:|:---:|
| Inkomend | openbare Load Balancer |
| Uitgaand | NAT-gateway |

Elke uitgaande configuratie van een regel of uitgaande regels wordt vervangen door NAT-gateway.  Inkomende is ontstaan wordt niet beïnvloed.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT en VM met openbaar IP-exemplaar op instantieniveau en openbare load balancer

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" width="425" title="Nat en VM op virtueel netwerk met openbaar IP-adres op instantieniveau en openbare load balancer">
</p>

*Figuur: Virtual Network NAT en VM met openbaar IP-adres op instantieniveau en openbare load balancer*

| Richting | Resource |
|:---:|:---:|
| Inkomend | VM met openbaar IP-exemplaar op instantieniveau en openbare load balancer |
| Uitgaand | NAT-gateway |

Elke uitgaande configuratie van een regel of uitgaande regels wordt vervangen door NAT-gateway.  De VM gebruikt ook NAT-gateway voor uitgaande.  Inkomende is ontstaan wordt niet beïnvloed.

### <a name="managing-basic-resources"></a>Basisbronnen beheren

Standaard load balancer, openbaar IP- en openbaar IP-voorvoegsel zijn compatibel met NAT-gateway. NAT-gateways werken in het bereik van een subnet. De basisSKU van deze services moet worden geïmplementeerd op een subnet zonder NAT-gateway. Deze scheiding maakt het mogelijk dat beide SKU-varianten naast elkaar bestaan in hetzelfde virtuele netwerk.

NAT-gateways hebben voorrang op uitgaande scenario's van het subnet. Basic load balancer of public IP (en elke managed service die ermee is gebouwd) kan niet worden aangepast met de juiste vertalingen. NAT-gateway neemt de controle over uitgaand naar internetverkeer op een subnet. Binnenkomend verkeer naar basisload balancer en openbare ip is niet beschikbaar. Binnenkomend verkeer naar een basisloadbalancer en of een openbaar ip-adres dat is geconfigureerd op een virtuele machine, is niet beschikbaar.

### <a name="availability-zones"></a>Beschikbaarheidszones

#### <a name="zone-isolation-with-zonal-stacks"></a>Zone isolatie met zonale stapels

<p align="center">
  <img src="media/nat-overview/az-directions.svg" width="425" title="Nat van virtueel netwerk met zoneisolatie, waardoor meerdere "zonal stacks"">
</p>

*Figuur: Virtual Network NAT met zoneisolatie, waarbij meerdere "zonale stapels" worden gemaakt*

Zelfs zonder beschikbaarheidszones is NAT veerkrachtig en kan het meerdere storingen in infrastructuuronderdelen overleven.  Beschikbaarheidszones bouwen voort op deze tolerantie met zoneisolatiescenario's voor NAT.

Virtuele netwerken en hun subnetten zijn regionale constructies.  Subnetten zijn niet beperkt tot een zone.

Er bestaat een zonale belofte voor zoneisolatie wanneer een virtuele machine-instantie die een NAT-gatewaybron gebruikt, zich in dezelfde zone bevindt als de NAT-gatewaybron en de openbare IP-adressen. Het patroon dat u wilt gebruiken voor zoneisolatie is het maken van een "zonale stack" per beschikbaarheidszone.  Deze "zonale stack" bestaat uit virtuele machine-exemplaren, NAT-gatewaybronnen, openbaar IP-adres en/of voorvoegselbronnen op een subnet waarvan wordt aangenomen dat deze alleen dezelfde zone bedient.   De bewerkingen en gegevensvlak worden vervolgens uitgelijnd met en beperkt tot de opgegeven zone. 

Als u niet in een andere zone dan waar uw scenario bestaat, wordt verwacht dat dit geen gevolgen heeft voor NAT. Uitgaand verkeer van virtuele machines in dezelfde zone mislukt vanwege zoneisolatie.  

#### <a name="integrating-inbound-endpoints"></a>Binnenkomende eindpunten integreren

Als voor uw scenario binnenkomende eindpunten vereist zijn, hebt u twee opties:

| Optie | Patroon | Voorbeeld | Pro | Con |
|---|---|---|---|---|
| (1) | **Lijn** de binnenkomende eindpunten uit met de respectievelijke **zonale stapels** die u maakt voor uitgaande. | Maak een standaard load balancer met zonale frontend. | Hetzelfde gezondheidsmodel en de foutmodus voor binnenkomend en uitgaand. Eenvoudiger te bedienen. | Individuele IP-adressen per zone moeten mogelijk worden gemaskeerd met een gemeenschappelijke DNS-naam. |
| (2) | **De** zonale stapels bedekken met een binnenkomend eindpunt **voor de verschillende zones.** | Maak een standaard load balancer met zone-redundante frontend. | Eén IP-adres voor binnenkomend eindpunt. | Verschillende gezondheidsmodel- en foutmodi voor binnenkomend en uitgaand.  Complexer om te werken. |

>[!NOTE]
> Voor een nat-gateway in de zone zijn IP-adressen vereist die overeenkomen met de zone van de NAT-gateway. NAT-gatewaybronnen met IP-adressen uit een andere zone of zonder zone zijn niet toegestaan.

#### <a name="cross-zone-outbound-scenarios-not-supported"></a>Uitgaande scenario's voor verschillende zones worden niet ondersteund

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" width="425" title="Nat van virtueel netwerk is niet compatibel met subnet met een zonespanning">
</p>

*Afbeelding: Nat van virtueel netwerk niet compatibel met subnet met zonespanning*

U een zonale belofte met NAT-gatewaybronnen niet bereiken wanneer virtuele machine-exemplaren in meerdere zones binnen hetzelfde subnet worden geïmplementeerd.   En zelfs als er meerdere zonale NAT-gateways aan een subnet waren gekoppeld, zou de virtuele machine-instantie niet weten welke NAT-gatewaybron moet worden geselecteerd.

Een zonale belofte bestaat niet wanneer a) de zone van een virtuele machine instantie en de zones van een zonale NAT gateway zijn niet uitgelijnd, of b) een regionale NAT gateway bron wordt gebruikt met zonale virtuele machine exemplaren.

Hoewel het scenario lijkt te werken, is het gezondheidsmodel en de foutmodus niet gedefinieerd vanuit het oogpunt van de beschikbaarheidszone. Overweeg te gaan met zonale stapels of alle regionale plaats.

>[!NOTE]
>De eigenschap zones van een NAT-gatewaybron is niet mutable.  Herschikken van NAT-gatewayresource met de beoogde regionale of zonevoorkeur.

>[!NOTE] 
>IP-adressen op zichzelf zijn niet zone-redundant als er geen zone is opgegeven.  De frontend van een [Standaard Load Balancer is zone-redundant](../load-balancer/load-balancer-standard-availability-zones.md#frontend) als er geen IP-adres wordt gemaakt in een specifieke zone.  Dit geldt niet voor NAT.  Alleen regionale of zone-isolatie wordt ondersteund.

## <a name="source-network-address-translation"></a>Vertaling van bronnetwerkadres

Source network address translation (SNAT) herschrijft de bron van een stroom om afkomstig te zijn van een ander IP-adres.  NAT-gatewaybronnen gebruiken een variant van SNAT die gewoonlijk wordt aangeduid als port address translation (PAT). PAT herschrijft het bronadres en de bronpoort. Met SNAT is er geen vaste relatie tussen het aantal privéadressen en hun vertaalde openbare adressen.  

### <a name="fundamentals"></a>Basisprincipes

Laten we eens kijken naar een voorbeeld van vier stromen om het basisconcept uit te leggen.  De NAT-gateway maakt gebruik van openbare IP-adresbron 65.52.0.2.

| Stroom | Bron tuple | Bestemming tuple |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Deze stromen kunnen er zo uitzien nadat PAT heeft plaatsgevonden:

| Stroom | Bron tuple | SNAT'ed bron tuple | Bestemming tuple | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.2:234 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.2:235 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.2:236 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:237 | 65.52.0.2:80 |

De bestemming ziet de bron van de stroom als 65.52.0.2 (SNAT brontuple) met de toegewezen poort weergegeven.  PAT zoals weergegeven in de vorige tabel wordt ook wel port maskerading SNAT.  Meerdere privébronnen worden vermommen achter een IP en poort.

Neem geen afhankelijkheid van de specifieke manier waarop bronpoorten zijn toegewezen.  Het voorgaande is slechts een illustratie van het fundamentele concept.

SNAT geleverd door NAT is anders dan [Load Balancer](../load-balancer/load-balancer-outbound-connections.md) in verschillende aspecten.

### <a name="on-demand"></a>On-demand

NAT biedt on-demand SNAT-poorten voor nieuwe uitgaande verkeersstromen. Alle beschikbare SNAT-poorten in voorraad worden gebruikt door elke virtuele machine op subnetten die zijn geconfigureerd met NAT. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" width="550" title="Virtual Network NAT on-demand uitgaande SNAT">
</p>

*Figuur: Virtual Network NAT on-demand uitgaande SNAT*

Elke IP-configuratie van een virtuele machine kan indien nodig on-demand uitgaande stromen on-demand maken.  Vooraf toewijzen, per instantieplanning inclusief overprovisioning per instantie, is niet vereist.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" width="550" title="Verschillen in uitputtingsscenario's">
</p>

*Figuur: Verschillen in uitputtingsscenario's*

Zodra een SNAT-poort wordt uitgebracht, is deze beschikbaar voor gebruik door elke virtuele machine op subnetten die zijn geconfigureerd met NAT.  On-demand toewijzing maakt dynamische en uiteenlopende workloads op subnet(s) mogelijk om SNAT-poorten te gebruiken die ze nodig hebben.  Zolang er SNAT-poortvoorraad beschikbaar is, zullen SNAT-stromen slagen. SNAT-poorthotspots profiteren in plaats daarvan van de grotere voorraad. SNAT-poorten worden niet ongebruikt gelaten voor virtuele machines die ze niet actief nodig hebben.

### <a name="scaling"></a>Schalen

Het schalen van NAT is in de eerste plaats een functie voor het beheren van de gedeelde, beschikbare SNAT-poortvoorraad. NAT heeft voldoende SNAT-poortvoorraad nodig voor verwachte piekuitgaande stromen voor alle subnetten die zijn gekoppeld aan een NAT-gatewaybron.  U openbare IP-adresbronnen, openbare IP-voorvoegselbronnen of beide gebruiken om SNAT-poortvoorraad te maken.

SNAT brengt privéadressen in kaart aan een of meer openbare IP-adressen, herschrijft bronadres en bronpoort in de processen. Een NAT-gatewaybron gebruikt 64.000 poorten (SNAT-poorten) per geconfigureerd openbaar IP-adres voor deze vertaling. NAT-gatewaybronnen kunnen worden opgeschaald naar 16 IP-adressen en 1M SNAT-poorten. Als er een openbare IP-voorvoegselbron wordt verstrekt, biedt elk IP-adres in het voorvoegsel snat-poortvoorraad. En het toevoegen van meer openbare IP-adressen verhoogt de beschikbare voorraad SNAT-poorten. TCP en UDP zijn afzonderlijke SNAT-poortvoorraden en zijn niet gerelateerd.

NAT-gatewaybronnen hergebruiken bronpoorten opportunistisch. Voor schaaldoeleinden moet u ervan uitgaan dat voor elke stroom een nieuwe SNAT-poort nodig is en moet u het totale aantal beschikbare IP-adressen voor uitgaand verkeer schalen.

### <a name="protocols"></a>Protocollen

NAT-gatewaybronnen werken samen met IP- en IP-transportheaders van UDP- en TCP-stromen en zijn agnostisch voor payloads van toepassingslagen.  Andere IP-protocollen worden niet ondersteund.

### <a name="timers"></a>Timers

>[!IMPORTANT]
>Lange idle timer kan onnodig verhogen kans op SNAT uitputting. Hoe langer een timer u opgeeft, hoe langer NAT-poorten vasthouden totdat ze uiteindelijk een time-out hebben. Als uw stromen niet actief zijn, zullen ze uiteindelijk toch mislukken en onnodig snat-poortvoorraad verbruiken.  Stromen die mislukken op 2 uur zou zijn mislukt bij de standaard 4 minuten ook. Het verhogen van de idle time-out is een laatste redmiddel optie die spaarzaam moet worden gebruikt. Als een stroom nooit inactief gaat, wordt deze niet beïnvloed door de niet-actieve timer.

TCP idle time-out kan worden aangepast van 4 minuten (standaard) tot 120 minuten (2 uur) voor alle stromen.  Bovendien u de niet-actieve timer opnieuw instellen met verkeer op de stroom.  Een aanbevolen patroon voor het vernieuwen van lange idle verbindingen en endpoint liveness detectie is TCP keepalives.  TCP-keepalives worden weergegeven als dubbele ACK's naar de eindpunten, zijn laag overhead en onzichtbaar voor de toepassingslaag.

De volgende timers worden gebruikt voor het vrijgeven van de SNAT-poort:

| Timer | Waarde |
|---|---|
| TCP FIN | 60 seconden |
| TCP RST | 10 seconden |
| TCP half open | 30 seconden |

Een SNAT-poort is na 5 seconden beschikbaar voor hergebruik naar hetzelfde doel-IP-adres en de doelpoort.

>[!NOTE] 
>Deze timerinstellingen kunnen worden gewijzigd. De waarden worden verstrekt om het oplossen van problemen te helpen en u moet op dit moment niet afhankelijk zijn van specifieke timers.

## <a name="limitations"></a>Beperkingen

- NAT is compatibel met standaard SKU public IP, public IP-voorvoegsel en load balancer-resources.   Basisresources (bijvoorbeeld basisload balancer) en alle producten die daaruit zijn afgeleid, zijn niet compatibel met NAT.  Basisresources moeten worden geplaatst op een subnet dat niet is geconfigureerd met NAT.
- IPv4-adresfamilie wordt ondersteund.  NAT heeft geen interactie met IPv6-adresfamilie.  NAT kan niet worden geïmplementeerd op een subnet met een IPv6-voorvoegsel.
- NSG-stroomlogboekregistratie wordt niet ondersteund bij het gebruik van NAT.
- NAT kan niet meerdere virtuele netwerken omvatten.


## <a name="feedback"></a>Feedback

We willen weten hoe we de service kunnen verbeteren. Ontbreekt er een mogelijkheid? Maak uw zaak voor wat we moeten bouwen volgende op [UserVoice voor NAT](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [nat van virtueel netwerk](nat-overview.md).
* Meer informatie over [statistieken en waarschuwingen voor NAT-gatewaybronnen](nat-metrics.md).
* Meer informatie over [het oplossen van PROBLEMEN met NAT-gatewaybronnen](troubleshoot-nat.md).
* Zelfstudie voor het valideren van NAT Gateway
  - [Azure-CLI](tutorial-create-validate-nat-gateway-cli.md)
  - [PowerShell](tutorial-create-validate-nat-gateway-powershell.md)
  - [Portal](tutorial-create-validate-nat-gateway-portal.md)
* Snel aan de slag voor het implementeren van een NAT-gatewaybron
  - [Azure-CLI](./quickstart-create-nat-gateway-cli.md)
  - [PowerShell](./quickstart-create-nat-gateway-powershell.md)
  - [Portal](./quickstart-create-nat-gateway-portal.md)
  - [Sjabloon](./quickstart-create-nat-gateway-template.md)
* Meer informatie over NAT-gatewaybron-API
  - [REST-API](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways)
  - [Azure-CLI](https://docs.microsoft.com/cli/azure/network/nat/gateway?view=azure-cli-latest)
  - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway)
* Meer informatie over [beschikbaarheidszones](../availability-zones/az-overview.md).
* Meer informatie over [standaard load balancer](../load-balancer/load-balancer-standard-overview.md).
* Meer informatie over [beschikbaarheidszones en standaard load balancer.](../load-balancer/load-balancer-standard-availability-zones.md)
* [Vertel ons wat we nu moeten bouwen voor Virtual Network NAT in UserVoice.](https://aka.ms/natuservoice)


