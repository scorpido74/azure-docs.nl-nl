---
title: Uitgaande verbindingen
titleSuffix: Azure Load Balancer
description: In dit artikel wordt uitgelegd hoe Azure virtuele machines kan communiceren met open bare Internet Services.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: contperfq1
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2020
ms.author: allensu
ms.openlocfilehash: 79399d0890f61d723f371528408d226f6a192ce4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336493"
---
# <a name="outbound-connections"></a>Uitgaande verbindingen

Azure Load Balancer biedt uitgaande connectiviteit via verschillende mechanismen. In dit artikel worden de scenario's beschreven en hoe u deze beheert. 

## <a name="outbound-connections-scenario-overview"></a><a name="scenarios"></a>Overzicht van scenario voor uitgaande verbindingen

De termen die in deze scenario's worden gebruikt. Voor meer informatie, Zie [terminologie](#terms):

* [Adres omzetting van bron netwerk (SNAT)](#snat)
* [Poort maskering (PAT)](#pat)
* Transmission Control Protocol (TCP)
* UDP (User Data gram Protocol)
* Network Address Translation
* Internet Control Message Protocol
* Encapsulating Security Protocol

### <a name="scenarios"></a>Scenario's

* [Scenario 1](#scenario1) : een virtuele machine met een openbaar IP-adres.
* [Scenario 2](#scenario2) : virtuele machine zonder openbaar IP-adres.
* [Scenario 3](#scenario3) : virtuele machine zonder openbaar IP-adres en zonder standaard Load Balancer.

### <a name="scenario-1---virtual-machine-with-public-ip"></a><a name="scenario1"></a>Scenario 1: een virtuele machine met een openbaar IP-adres

| Lidkoppelingen | Methode | IP-protocollen |
| ---------- | ------ | ------------ |
| Openbaar load balancer of zelfstandig | [SNAT](#snat) </br> [Poort maskering](#pat) niet gebruikt. | TCP </br> UDP </br> ICMP </br> PROTOCOLSPECIFIEKE |

#### <a name="description"></a>Beschrijving

Azure gebruikt het open bare IP-adres dat is toegewezen aan de IP-configuratie van de NIC van het exemplaar voor alle uitgaande stromen. Alle tijdelijke poorten zijn beschikbaar voor het exemplaar. Het maakt niet uit of de virtuele machine gelijkmatig is verdeeld of niet. Dit scenario heeft voor rang op de andere. 

Een openbaar IP-adres dat is toegewezen aan een virtuele machine is een 1:1-relatie (in plaats van 1: veel) en geïmplementeerd als een stateless 1:1 NAT.

### <a name="scenario-2---virtual-machine-without-public-ip"></a><a name="scenario2"></a>Scenario 2: virtuele machine zonder openbaar IP-adres

| Lidkoppelingen | Methode | IP-protocollen |
| ------------ | ------ | ------------ |
| Openbare load balancer | Gebruik van load balancer frontend voor [SNAT](#snat) met [poort maskering (Pat)](#pat).| TCP </br> UDP |

#### <a name="description"></a>Beschrijving

De load balancer resource is geconfigureerd met een load balancer regel. Deze regel wordt gebruikt om een koppeling tussen de open bare IP-frontend te maken met de back-end-pool. 

Als u deze regel configuratie niet voltooit, wordt het gedrag in scenario 3 beschreven. 

Een regel met een listener is niet vereist voor het slagen van de status test.

Wanneer een virtuele machine een uitgaande stroom maakt, wordt het IP-adres van de bron door Azure vertaald naar het open bare IP-adres van de open bare frontend van load balancer. Deze vertaling wordt uitgevoerd via [SNAT](#snat). 

Tijdelijke poorten van het open bare IP-adres van de load balancer frontend worden gebruikt om onderscheid te maken tussen afzonderlijke stromen die afkomstig zijn van de virtuele machine. SNAT maakt dynamisch gebruik van [vooraf toegewezen tijdelijke poorten](#preallocatedports) wanneer uitgaande stromen worden gemaakt. 

In deze context worden de tijdelijke poorten die worden gebruikt voor SNAT de SNAT-poorten genoemd. De SNAT-poorten worden vooraf toegewezen, zoals wordt beschreven in de [standaard toewijzings tabel](#snatporttable)voor de SNAT-poorten.

### <a name="scenario-3---virtual-machine-without-public-ip-and-without-standard-load-balancer"></a><a name="scenario3"></a> Scenario 3: virtuele machine zonder openbaar IP-adres en zonder standaard load balancer

| Lidkoppelingen | Methode | IP-protocollen |
| ------------ | ------ | ------------ |
|Geen </br> Basis load balancer | [SNAT](#snat) met [poort maskering (Pat)](#pat)| TCP </br> UDP | 

#### <a name="description"></a>Beschrijving

Wanneer de virtuele machine een uitgaande stroom maakt, vertaalt Azure het bron-IP-adres van de uitgaande stroom naar een IP-adres van een open bare bron. Dit open bare IP-adres kan **niet worden geconfigureerd** en kan niet worden gereserveerd. Dit adres telt niet op basis van de open bare IP-resource limiet van het abonnement. 

Het open bare IP-adres wordt vrijgegeven en er is een nieuwe open bare IP aangevraagd als u de volgende handelingen opnieuw implementeert: 

* Virtuele machine
* Beschikbaarheidsset
* Schaalset voor virtuele machines  

Gebruik dit scenario niet voor het toevoegen van IP-adressen aan een acceptatie lijst. Gebruik scenario 1 of 2 waar u het uitgaande gedrag expliciet declareert. De [SNAT](#snat) -poorten zijn vooraf toegewezen, zoals wordt beschreven in de [standaard toewijzings tabel](#snatporttable)voor de SNAT-poorten.



## <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>Poort toewijzings algoritme

Azure gebruikt een algoritme om te bepalen hoeveel vooraf toegewezen [SNAT](#snat) -poorten beschikbaar zijn. Met het algoritme wordt het aantal poorten gebaseerd op de grootte van de back-end-pool. 

Voor elk openbaar IP-adres dat is gekoppeld aan een load balancer, zijn 64.000 poorten beschikbaar als [SNAT](#snat) -poorten. Hetzelfde aantal [SNAT](#snat) -poorten is vooraf toegewezen voor UDP en TCP. De poorten worden onafhankelijk per IP-protocol verbruikt. 

Het [SNAT](#snat) -poort gebruik verschilt, afhankelijk van of de stroom UDP of TCP is. 

Poorten worden dynamisch verbruikt tot de vooraf toegewezen limiet.  De poorten worden vrijgegeven wanneer de stroom wordt gesloten of er treedt een time-out voor inactiviteit op.

Zie [problemen met uitgaande verbindingen in azure Load Balancer oplossen](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) voor meer informatie over time-outs voor inactiviteit 

Poorten worden alleen gebruikt als het nodig is om stromen uniek te maken.

### <a name="dynamic-snat-ports-preallocation"></a><a name="snatporttable"></a> Voorafgaande toewijzing van dynamische SNAT-poorten

In de volgende tabel ziet u de [SNAT](#snat) -poort voortoewijzingen voor lagen van back-endadresgroep-Pools:

| Pool grootte (VM-exemplaren) | Vooraf toegewezen SNAT-poorten per IP-configuratie |
| --- | --- |
| 1-50 | 1.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

Het wijzigen van de grootte van uw back-end-pool kan invloed hebben op sommige van uw vastgelegde stromen:

- Met de back-end-pool grootte worden de trigger overgangen in de volgende laag verhoogd. De helft van de vooraf toegewezen [SNAT](#snat) -poorten worden vrijgemaakt tijdens de overgang naar de volgende laag. 

- Stromen die zijn gekoppeld aan een teruggewonnen time-out voor de [SNAT](#snat) -poort en sluiten. Deze stromen moeten opnieuw tot stand worden gebracht. Als er een nieuwe stroom wordt gedaan, wordt de stroom onmiddellijk uitgevoerd zolang er vooraf toegewezen poorten beschikbaar zijn.

- Als de back-end-pool grootte lager en overgangen naar een lagere laag is, neemt het aantal beschik bare [SNAT](#snat) -poorten toe. De bestaande opgegeven [SNAT](#snat) -poorten en hun respectieve stromen worden niet beïnvloed.

## <a name="outbound-rules"></a><a name="outboundrules"></a>Uitgaande regels

 Met uitgaande regels kan de uitgaande Network Address Translation van de open bare [standaard Load Balancer](load-balancer-standard-overview.md)worden geconfigureerd.  

> [!NOTE]
> **Azure Virtual Network NAT** kan uitgaande connectiviteit bieden voor virtuele machines in een virtueel netwerk.  Zie [Wat is Azure Virtual Network NAT?](../virtual-network/nat-overview.md) voor meer informatie.

U hebt volledige declaratieve controle over uitgaande connectiviteit om deze mogelijkheid te schalen en af te stemmen op uw behoeften. In deze sectie wordt scenario 2 uitgevouwen, zoals hierboven wordt beschreven.

![Uitgaande regels van Load Balancer](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Met uitgaande regels kunt u load balancer de uitgaande NAT helemaal opnieuw te definiëren. U kunt ook het gedrag van bestaande uitgaande NAT schalen en afstemmen.

Met uitgaande regels kunt u het volgende beheren:

- Welke virtuele machines moeten worden vertaald naar de open bare IP-adressen.
- Hoe uitgaande [SNAT](#snat) -poorten moeten worden opgegeven.
- De protocollen waarvoor een uitgaande vertaling moet worden geboden.
- Welke duur moet worden gebruikt voor de time-out voor uitgaande verbindingen (4-120 minuten).
- Of een TCP-Reset moet worden verzonden bij een time-out voor inactiviteit
- TCP-en UDP-transport protocollen met één regel

### <a name="outbound-rule-definition"></a>Definitie van uitgaande regel

Uitgaande regels volgen dezelfde vertrouwde syntaxis als taak verdeling en binnenkomende NAT-regels: **frontend**-  +  **para meters**  +  **back-end-pool**. Met een uitgaande regel wordt uitgaande NAT geconfigureerd voor _alle virtuele machines die door de back-endadresgroep worden geïdentificeerd_ om te worden vertaald naar de front- _End_.  De _para meters_ bieden extra nauw keurige controle over de uitgaande NAT-algoritme.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Uitgaand NAT schalen met meerdere IP-adressen

Elk extra IP-adres dat wordt geleverd door een front-end biedt extra 64.000 tijdelijke poorten voor load balancer die als SNAT-poorten kunnen worden gebruikt. 

Gebruik meerdere IP-adressen voor het plannen van grootschalige scenario's. Gebruik regels voor uitgaande verbindingen om de [SNAT-uitputting](troubleshoot-outbound-connection.md#snatexhaust)te verhelpen. 

U kunt ook een [openbaar IP-voor voegsel](https://aka.ms/lbpublicipprefix) rechtstreeks met een uitgaande regel gebruiken. 

Een openbaar IP-voor voegsel verg root de schaal van uw implementatie. Het voor voegsel kan worden toegevoegd aan de lijst met toegestane stromen die afkomstig zijn van uw Azure-resources. U kunt een front-end-IP-configuratie in de load balancer configureren om te verwijzen naar een openbaar IP-adres voorvoegsel.  

De load balancer heeft controle over het open bare IP-voor voegsel. Voor de uitgaande regel worden automatisch alle open bare IP-adressen gebruikt die zich in het open bare IP-voor voegsel voor uitgaande verbindingen bevinden. 

Elk van de IP-adressen binnen het open bare IP-voor voegsel biedt een extra 64.000 tijdelijke poorten per IP-adres voor load balancer om te gebruiken als SNAT-poorten.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Uitgaande time-out voor niet-actieve stroom en TCP Reset

Uitgaande regels bieden een configuratie parameter voor het beheren van de uitgaande time-out voor niet-actieve stroom en om deze te vergelijken met de behoeften van uw toepassing. Uitgaande time-outs voor inactiviteit zijn standaard ingesteld op 4 minuten. Zie een [time-out voor inactiviteit configureren](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout)voor meer informatie. 

Het standaard gedrag van load balancer is om de stroom op de achtergrond neer te zetten wanneer de uitgaande time-out voor inactiviteit is bereikt. De `enableTCPReset` para meter maakt een voorspelbaar toepassings gedrag en-beheer mogelijk. De para meter bepaalt of bidirectionele TCP Reset (TCP RST) moet worden verzonden bij de time-out van de uitgaande time-out voor inactiviteit. 

[TCP Reset controleren bij time-out voor inactiviteit](https://aka.ms/lbtcpreset) voor meer informatie over de beschik baarheid van regio's.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Uitgaande connectiviteit voor komen

Taakverdelings regels bieden automatische programmering van uitgaande NAT. Sommige scenario's profiteren of vereisen dat u de automatische programmering van uitgaande NAT door de taakverdelings regel uitschakelt. Door via de regel uit te scha kelen, kunt u het gedrag beheren of verfijnen.  

U kunt deze para meter op twee manieren gebruiken:

1. Het inkomende IP-adres voor uitgaande SNAT wordt voor komen. Schakel uitgaande SNAT uit in de taakverdelings regel.
  
2. Stem de uitgaande [SNAT](#snat) -para meters af van een IP-adres dat tegelijkertijd wordt gebruikt voor inkomend en uitgaand. De automatische uitgaande NAT moet worden uitgeschakeld zodat een regel voor uitgaand verkeer kan worden beheerd. Als u de toewijzing van de SNAT-poort van een adres dat ook wordt gebruikt voor inkomend, wilt wijzigen, `disableOutboundSnat` moet u de para meter instellen op True. 

De bewerking voor het configureren van een uitgaande regel mislukt als u probeert een IP-adres te definiëren dat wordt gebruikt voor inkomend verkeer.  Schakel eerst de uitgaande NAT van de taakverdelings regel uit.

>[!IMPORTANT]
> De virtuele machine heeft geen uitgaande verbinding als u deze para meter instelt op True en geen uitgaande regel hebt om uitgaande connectiviteit te definiëren.  Sommige bewerkingen van uw virtuele machine of uw toepassing kunnen afhankelijk zijn van een uitgaande verbinding. Zorg ervoor dat u bekend bent met de afhankelijkheden van uw scenario en dat dit gevolgen heeft voor het maken van deze wijziging.

Soms is het niet wenselijk dat een virtuele machine een uitgaande stroom maakt. Er is mogelijk een vereiste voor het beheren van welke doelen uitgaande stromen worden ontvangen of die doelen beginnen met binnenkomende stromen. Gebruik [netwerk beveiligings groepen](../virtual-network/security-overview.md) om de doelen te beheren die de virtuele machine bereikt. Gebruik Nsg's om te beheren welke open bare bestemmingen binnenkomende stromen starten.

Wanneer u een NSG toepast op een virtuele machine met taak verdeling, moet u rekening houden met de [service Tags](../virtual-network/security-overview.md#service-tags) en de [standaard beveiligings regels](../virtual-network/security-overview.md#default-security-rules). Zorg ervoor dat de VM Health probe-aanvragen van Azure Load Balancer kan ontvangen.

Als een NSG de status test aanvragen blokkeert vanuit het standaard label AZURE_LOADBALANCER, mislukt de VM-status test en wordt de virtuele machine gemarkeerd. Load Balancer stopt met het verzenden van nieuwe stromen naar die virtuele machine.

## <a name="scenarios-with-outbound-rules"></a>Scenario's met uitgaande regels

### <a name="outbound-rules-scenarios"></a>Scenario's voor uitgaande regels

* [Scenario 1](#scenario1out) : uitgaande verbindingen configureren voor een specifieke set open bare ip's of voor voegsel.
* [Scenario 2](#scenario2out) : toewijzing van de [SNAT](#snat) -poort wijzigen.
* [Scenario 3](#scenario3out) : alleen uitgaand verkeer inschakelen.
* [Scenario 4](#scenario4out) : uitgaand NAT-verkeer alleen voor vm's (geen inkomend).
* [Scenario 5](#scenario5out) : uitgaand NAT voor interne standaard Load Balancer.
* [Scenario 6](#scenario6out) : Schakel TCP-& UDP-protocollen in voor uitgaande nat met een open bare standaard Load Balancer.

### <a name="scenario-1"></a><a name="scenario1out"></a>Scenario 1

| Scenario |
| -------- |
| Uitgaande verbindingen configureren voor een specifieke set open bare Ip's of voor voegsel|

#### <a name="details"></a>Details

Gebruik dit scenario om uitgaande verbindingen op basis van een set open bare IP-adressen aan te passen. Open bare Ip's of voor voegsels toevoegen aan een lijst voor toestaan of weigeren op basis van de oorsprong.

Dit open bare IP-adres of voor voegsel kan hetzelfde zijn als het gebruik van een regel voor taak verdeling. 

Een ander openbaar IP-adres of-voor voegsel gebruiken dan wordt gebruikt door een regel voor taak verdeling:  

1. Openbaar IP-voor voegsel of openbaar IP-adres maken.
2. Een open bare standaard load balancer maken 
3. Een frontend maken die verwijst naar het open bare IP-voor voegsel of het open bare IP-adres dat u wilt gebruiken. 
4. Hergebruik een back-end-pool of maak een back-end-pool en plaats de virtuele machines in een back-end-groep van de open bare load balancer
5. Configureer een uitgaande regel op de open bare load balancer om uitgaande NAT voor de virtuele machines in te scha kelen met behulp van de front-end. Als u niet wilt dat de taakverdelings regel voor uitgaand verkeer wordt gebruikt, schakelt u uitgaande SNAT uit op de regel voor taak verdeling.

### <a name="scenario-2"></a><a name="scenario2out"></a>Scenario 2

| Scenario |
| -------- |
| Toewijzing van [SNAT](#snat) -poort wijzigen |

#### <a name="details"></a>Details

U kunt uitgaande regels gebruiken voor het afstemmen van de [automatische toewijzing van de SNAT-poort op basis van de back-endadresgroep](load-balancer-outbound-connections.md#preallocatedports). 

Als u een SNAT-uitputting ondervindt, verhoogt u het aantal [SNAT](#snat) -poorten dat is opgegeven in de standaard waarde van 1024. 

Elk openbaar IP-adres draagt bij aan Maxi maal 64.000 tijdelijke poorten. Het aantal virtuele machines in de back-endadresgroep bepaalt het aantal poorten dat naar elke VM wordt gedistribueerd. Een virtuele machine in de back-end-pool heeft toegang tot Maxi maal 64.000 poorten. Voor twee virtuele machines kunnen Maxi maal 32.000 [SNAT](#snat) -poorten worden opgegeven met een uitgaande regel (2x 32.000 = 64.000). 

U kunt regels voor uitgaande verbindingen gebruiken voor het afstemmen van de maximale SNAT-poorten. U geeft meer of minder dan de standaard toewijzing van de [SNAT](#snat) -poort. Elk openbaar IP-adres van een front-end van een uitgaande regel draagt bij aan Maxi maal 64.000 tijdelijke poorten voor gebruik als [SNAT](#snat) -poorten.  

Load Balancer geeft [SNAT](#snat) -poorten in veelvouden van 8. Als u een waarde opgeeft die niet deelbaar is door 8, wordt de configuratie bewerking geweigerd. 

Als u meer [SNAT](#snat) -poorten probeert te geven dan beschikbaar zijn op basis van het aantal open bare IP-adressen, wordt de configuratie bewerking geweigerd.

Als u 10.000 poorten per VM en zeven Vm's in een back-end-groep met één openbaar IP-adres deelt, wordt de configuratie geweigerd. Zeven vermenigvuldigd met 10.000 overschrijdt de limiet voor 64.000 poorten. Voeg meer open bare IP-adressen toe aan de front-end van de regel voor uitgaande verbindingen om het scenario in te scha kelen. 

Keer terug naar de [standaard poort toewijzing](load-balancer-outbound-connections.md#preallocatedports) door 0 op te geven voor het aantal poorten. De eerste 50 VM-exemplaren krijgen 1024 poorten, 51-100 VM-instanties krijgen 512 tot Maxi maal het maximum aantal exemplaren.  Zie [hierboven](#snatporttable)voor meer informatie over de standaard toewijzing van de SNAT-poort.

### <a name="scenario-3"></a><a name="scenario3out"></a>Scenario 3

| Scenario |
| -------- |
| Alleen uitgaand verkeer inschakelen |

#### <a name="details"></a>Details

U kunt een open bare standaard load balancer gebruiken om uitgaande NAT te bieden voor een groep Vm's. In dit scenario gebruikt u een regel voor uitgaande verbindingen, zonder dat er extra regels nodig zijn.

> [!NOTE]
> **Azure Virtual Network NAT** kan uitgaande connectiviteit bieden voor virtuele machines zonder dat hiervoor een Load Balancer nodig is.  Zie [Wat is Azure Virtual Network NAT?](../virtual-network/nat-overview.md) voor meer informatie.

### <a name="scenario-4"></a><a name="scenario4out"></a>Scenario 4

| Scenario |
| -------- |
| Alleen uitgaand NAT voor Vm's (geen inkomend) |

> [!NOTE]
> **Azure Virtual Network NAT** kan uitgaande connectiviteit bieden voor virtuele machines zonder dat hiervoor een Load Balancer nodig is.  Zie [Wat is Azure Virtual Network NAT?](../virtual-network/nat-overview.md) voor meer informatie.

#### <a name="details"></a>Details

Voor dit scenario:

1. Maak een openbaar IP-adres of een voor voegsel.
2. Maak een open bare standaard load balancer. 
3. Maak een front-end dat is gekoppeld aan het open bare IP-adres of het voor voegsel dat voor uitgaand
4. Maak een back-end-pool voor de Vm's.
5. Plaats de virtuele machines in de back-end-pool.
6. Een uitgaande regel configureren om uitgaande NAT in te scha kelen.

Gebruik een voor voegsel of openbaar IP-adres om [SNAT](#snat) -poorten te schalen. Voeg de bron van uitgaande verbindingen toe aan een lijst voor toestaan of weigeren.

### <a name="scenario-5"></a><a name="scenario5out"></a>Scenario 5

| Scenario |
| -------- |
| Uitgaand NAT voor interne standaard load balancer |

> [!NOTE]
> **Azure Virtual Network NAT** kan uitgaande connectiviteit bieden voor virtuele machines die gebruikmaken van een interne standaard Load Balancer.  Zie [Wat is Azure Virtual Network NAT?](../virtual-network/nat-overview.md) voor meer informatie.

#### <a name="details"></a>Details

Uitgaande connectiviteit is niet beschikbaar voor een interne standaard load balancer totdat deze expliciet is gedeclareerd. 

Zie voor meer informatie [Load Balancer configuratie voor alleen-uitgaand verkeer](https://docs.microsoft.com/azure/load-balancer/egress-only).


### <a name="scenario-6"></a><a name="scenario6out"></a>Scenario 6

| Scenario |
| -------- |
| Schakel TCP & UDP-protocollen in voor uitgaande NAT met een open bare standaard load balancer |

#### <a name="details"></a>Details

Wanneer u een open bare standaard load balancer gebruikt, komt de automatische uitgaande NAT die overeenkomt met het transport protocol van de taakverdelings regel. 

1. Schakel uitgaande [SNAT](#snat) uit op de taakverdelings regel. 
2. Een uitgaande regel configureren op hetzelfde load balancer.
3. Gebruik de back-end-groep die al door uw virtuele machines wordt gebruikt. 
4. Geef ' Protocol ': ' all ' op als onderdeel van de uitgaande regel. 

Wanneer alleen binnenkomende NAT-regels worden gebruikt, wordt er geen uitgaand NAT-verkeer gegeven. 

1. Virtuele machines in een back-end-groep plaatsen.
2. Een of meer frontend-IP-configuraties met open bare IP-adressen of open bare IP-voor voegsel definiëren 
3. Een uitgaande regel configureren op hetzelfde load balancer. 
4. Geef ' Protocol ': ' all ' op als onderdeel van de uitgaande regel

## <a name="terminology"></a><a name="terms"></a> Terminologie

### <a name="source-network-address-translation"></a><a name="snat"></a>Bron Network Address Translation

| Toepasselijke Protocol (s) |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>Details

Een implementatie in azure kan communiceren met eind punten buiten Azure in de open bare IP-adres ruimte.

Wanneer een instantie uitgaand verkeer naar een bestemming met een openbaar IP-adres start, wijst Azure het privé-IP-adres van de resource dynamisch toe aan een openbaar IP-adres. 

Nadat deze toewijzing is gemaakt, bereikt het retour verkeer voor deze uitgaande stroom van het privé-IP-adres waar de stroom vandaan komt. 

Azure gebruikt **bron Network Address Translation (SNAT)** om deze functie uit te voeren.

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Poort maskering SNAT (PAT)

| Toepasselijke Protocol (s) |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>Details

Als privé Ip's zich achter één openbaar IP-adres bevinden, gebruikt Azure **poort adres omzetting (Pat)** om de privé-IP-adressen te verbergen. 

Tijdelijke poorten worden gebruikt voor PAT en zijn [vooraf toegewezen](#preallocatedports) op basis van pool grootte. 

Wanneer een open bare load balancer is gekoppeld aan Vm's zonder open bare IP-adressen, wordt elke uitgaande verbindings bron herschreven. 

De bron wordt herschreven van het privé-IP-adres van het virtuele netwerk naar het open bare IP-adres van de load balancer. 

In de open bare IP-adres ruimte moet de vijf tupel van de onderstaande stroom uniek zijn:

* IP-adres van bron
* Bronpoort
* IP-transport protocol
* IP-adres van doel
* Doelpoort 

Het SNAT-poort masker kan worden gebruikt met TCP-of UDP-protocollen. SNAT-poorten worden gebruikt na het herschrijven van het IP-adres van de privé bron, omdat meerdere stromen afkomstig zijn van één openbaar IP-adres. De SNAT-algoritme voor poort maskering geeft de maximale SNAT-poorten voor UDP versus TCP.

### <a name="snat-ports-tcp"></a>SNAT-poorten (TCP)

| Toepasselijke Protocol (s) |
|------------------------|
| TCP |

#### <a name="details"></a>Details

SNAT-poorten zijn tijdelijke poorten beschikbaar voor een openbaar IP-bron adres. Eén SNAT-poort wordt per stroom verbruikt naar één doel-IP-adres en-poort. 

Voor meerdere TCP-stromen naar hetzelfde doel-IP-adres, dezelfde poort en hetzelfde protocol gebruikt elke TCP-stroom één SNAT-poort. 

Dit verbruik zorgt ervoor dat de stromen uniek zijn wanneer ze afkomstig zijn uit hetzelfde open bare IP-adres en onderweg naar de:

* Hetzelfde doel-IP-adres
* Poort
* Protocol 

Meerdere stromen delen één SNAT-poort. 

Het doel-IP-adres, de poort en het Protocol maken stroom uniek zonder dat er extra bron poorten nodig zijn om stromen te onderscheiden van de open bare IP-adres ruimte.


### <a name="snat-ports-udp"></a>SNAT-poorten (UDP)

| Toepasselijke Protocol (s) |
|------------------------|
| UDP |

#### <a name="details"></a>Details

UDP SNAT-poorten worden beheerd door een ander algoritme dan TCP-SNAT-poorten.  Voor de Load Balancer wordt een algoritme gebruikt met de naam Port-restricted kegel NAT voor UDP.

Er wordt één SNAT-poort gebruikt voor het doel-IP-adres en de poort voor elke stroom.


### <a name="exhaustion"></a><a name="exhaustion"></a>Uitputting

| Toepasselijke Protocol (s) |
|------------------------|
| N.v.t. |

#### <a name="details"></a>Details

Wanneer de SNAT-poort resources zijn uitgeput, mislukken uitgaande stromen totdat bestaande stromen van SNAT-poorten worden vrijgegeven. De Load Balancer verwijdert de SNAT-poorten wanneer de stroom wordt gesloten.

Een [time-out voor inactiviteit](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) van vier minuten wordt gebruikt door de load BALANCER om SNAT-poorten vrij te maken.

UDP SNAT-poorten lopen doorgaans veel sneller dan TCP SNAT-poorten vanwege het verschil in gebruikte algoritmen. Ontwerp-en schaal test vanwege dit verschil.

### <a name="snat-port-release-behavior-tcp"></a>Release van het SNAT-poort (TCP)

| Toepasselijke Protocol (s) |
|------------------------|
| TCP |

#### <a name="details"></a>Details

Wanneer een server of client een FINACK verzendt, wordt na 240 seconden een SNAT-poort vrijgegeven. In het geval dat een RST wordt gezien, wordt een SNAT-poort na 15 seconden vrijgegeven. Als de time-out voor inactiviteit is bereikt, wordt de poort vrijgegeven.

### <a name="snat-port-release-behavior-udp"></a>Release gedrag van de SNAT-poort (UDP)

| Toepasselijke Protocol (s) |
|------------------------|
| TCP |

#### <a name="details"></a>Details

Als er een time-out voor inactiviteit is bereikt, wordt de poort vrijgegeven.

### <a name="snat-port-reuse"></a>SNAT-poort opnieuw gebruiken

| Toepasselijke Protocol (s) |
|------------------------|
| TCP </br> UDP |

#### <a name="details"></a>Details

Zodra een poort is vrijgegeven, is de poort beschikbaar voor hergebruik. De maximale beschik bare SNAT-poorten voor een bepaald scenario zijn een volg orde van laag naar hoog.

## <a name="limitations"></a>Beperkingen

- Het maximale aantal bruikbare tijdelijke poorten per frontend-IP-adres is 64.000.
- Het bereik van de Configureer bare uitgaande time-out voor inactiviteit is 4 tot 120 minuten (240 tot 7200 seconden).
- De Load Balancer biedt geen ondersteuning voor ICMP voor uitgaande NAT.
- Uitgaande regels kunnen alleen worden toegepast op de primaire IP-configuratie van een NIC.  U kunt geen uitgaande regel maken voor het secundaire IP-adres van een virtuele machine of NVA. Er worden meerdere Nic's ondersteund.
- Web Worker-rollen zonder een virtueel netwerk en andere micro soft-platform Services kunnen toegankelijk zijn wanneer een interne standaard load balancer wordt gebruikt. Deze toegankelijkheid is vanwege een neven effect van de werking van de prevnet-Services en andere platform Services. Vertrouw niet op deze zijde als de respectieve service zelf of het onderliggende platform kan zonder kennisgeving worden gewijzigd. Stel altijd dat u een uitgaande connectiviteit expliciet moet maken als u alleen een interne standaard load balancer wilt gebruiken. Scenario 3 dat in dit artikel wordt beschreven, is niet beschikbaar.

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt met uitgaande verbindingen via een Azure Load Balancer, raadpleegt u de [hand leiding voor het oplossen van problemen met uitgaande verbindingen](../load-balancer/troubleshoot-outbound-connection.md).

- Meer informatie over [standaard Load Balancer](load-balancer-standard-overview.md).
- Bekijk onze [Veelgestelde vragen over Azure Load Balancer](load-balancer-faqs.md).
- Meer informatie over [Uitgaande regels](load-balancer-outbound-rules-overview.md) voor standaard open bare Load Balancer.

