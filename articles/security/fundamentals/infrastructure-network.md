---
title: Azure-netwerkarchitectuur
description: In dit artikel vindt u een algemene beschrijving van het Microsoft Azure-infrastructuurnetwerk.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2019
ms.author: terrylan
ms.openlocfilehash: c4756c36c2243840df69f3696e7ddac3628f3a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727177"
---
# <a name="azure-network-architecture"></a>Azure-netwerkarchitectuur
De Azure-netwerkarchitectuur volgt een aangepaste versie van het industriestandaard core/distribution/access-model, met verschillende hardwarelagen. De lagen omvatten:

- Core (datacenterrouters)
- Distributie (toegangsrouters en L2-aggregatie). De distributielaag scheidt L3-routering van L2-schakelen.
- Toegang (L2-hostswitches)

De netwerkarchitectuur heeft twee niveaus van laag 2-switches. De ene laag verzamelt verkeer van de andere laag. De tweede laag lussen om redundantie op te nemen. De architectuur zorgt voor een flexibelere VLAN-voetafdruk en verbetert de poortschaling. De architectuur houdt L2 en L3 verschillend, waardoor het gebruik van hardware in elk van de verschillende lagen in het netwerk mogelijk is en de fout in één laag wordt geminimaliseerd om de andere laag(en) te beïnvloeden. Het gebruik van trunks maakt het delen van resources mogelijk, zoals de connectiviteit met de L3-infrastructuur.

## <a name="network-configuration"></a>Netwerkconfiguratie
De netwerkarchitectuur van een Azure-cluster binnen een datacenter bestaat uit de volgende apparaten:

- Routers (datacenter, toegangsrouter en border leaf-routers)
- Switches (aggregatie en top-of-rack switches)
- Digi CMs
- Stroomverdelingseenheden

Azure heeft twee afzonderlijke architecturen. Sommige bestaande Azure-klanten en gedeelde services bevinden zich op de standaard LAN-architectuur (DLA), terwijl nieuwe regio's en virtuele klanten zich bevinden in de Quantum 10 -architectuur (Q10). De DLA-architectuur is een traditioneel boomontwerp, met actieve/passieve toegangsrouters en acl.n.s.- De Quantum 10-architectuur is een Close/mesh-ontwerp van routers, waarbij ACL's niet worden toegepast op de routers. In plaats daarvan worden ACL's toegepast onder de routering, via Software Load Balancing (SLB) of software defined VLAN's.

Het volgende diagram biedt een overzicht op hoog niveau van de netwerkarchitectuur binnen een Azure-cluster:

![Diagram van Azure-netwerk](./media/infrastructure-network/network-arch.png)

### <a name="quantum-10-devices"></a>Quantum 10-apparaten
Het Quantum 10-ontwerp voert laag 3-schakelen uit, verspreid over meerdere apparaten in een Clos/mesh-ontwerp. De voordelen van het Q10-ontwerp omvatten een grotere capaciteit en een grotere mogelijkheid om bestaande netwerkinfrastructuur te schalen. Het ontwerp maakt gebruik van border leaf routers, spine switches en top-of-rack routers om verkeer door te geven aan clusters over meerdere routes, waardoor fouttolerantie mogelijk is. Software load balancing, in plaats van hardware-apparaten, behandelt beveiligingsservices zoals netwerkadres vertaling.

### <a name="access-routers"></a>Access-routers
De Distributie/Access L3-routers (AR's) voeren de primaire routeringsfunctionaliteit uit voor de distributie- en toegangslagen. Deze apparaten worden geïmplementeerd als een paar en zijn de standaardgateway voor subnetten. Elk AR-paar kan meerdere L2-aggregatieschakelparen ondersteunen, afhankelijk van de capaciteit. Het maximale aantal is afhankelijk van de capaciteit van het apparaat en de foutdomeinen. Een typisch getal is drie L2 aggregatieswitchparen per AR-paar.

### <a name="l2-aggregation-switches"></a>L2-aggregatieswitches  
Deze apparaten dienen als aggregatiepunt voor L2-verkeer. Zij zijn de distributielaag voor de L2-stof en kunnen grote hoeveelheden verkeer verwerken. Omdat deze apparaten verkeer samenvoegen, vereisen ze 802.1q-functionaliteit en technologieën met hoge bandbreedte, zoals poortaggregatie en 10GE.

### <a name="l2-host-switches"></a>L2-hostschakelaars
Hosts maken rechtstreeks verbinding met deze switches. Het kunnen rack-mounted switches zijn, of chassis implementaties. De 802.1q standaard maakt het mogelijk voor de aanwijzing van een VLAN als een native VLAN, behandelen dat VLAN als normale (untagged) Ethernet framing. Onder normale omstandigheden worden frames op de inheemse VLAN verzonden en ongemerkt ontvangen op een 802.1q trunk port. Deze functie is ontworpen voor migratie naar 802.1q en compatibiliteit met apparaten die niet geschikt zijn voor niet-802.1q. In deze architectuur maakt alleen de netwerkinfrastructuur gebruik van de native VLAN.

Deze architectuur specificeert een standaard voor native VLAN-selectie. De standaard zorgt er, waar mogelijk, voor dat de AR-apparaten een unieke, native VLAN hebben voor elke trunk en de L2Aggregation naar L2Aggregation trunks. De L2Aggregation naar L2Host Switch trunks hebben een niet-standaard native VLAN.

### <a name="link-aggregation-8023ad"></a>Koppelingsaggregatie (802.3ad)
Link aggregatie maakt het mogelijk om meerdere afzonderlijke koppelingen samen te voegen en te behandelen als één logische koppeling. Om operationele foutopsporing te vergemakkelijken, moet het aantal dat wordt gebruikt om poortkanaalinterfaces aan te wijzen, worden gestandaardiseerd. De rest van het netwerk gebruikt hetzelfde nummer aan beide uiteinden van een poortkanaal.

De nummers die zijn opgegeven voor de L2Agg-switch naar L2Host zijn de poortkanaalnummers die aan de L2Agg-zijde worden gebruikt. Omdat het bereik van nummers is beperkter aan de L2Host kant, de standaard is het gebruik van nummers 1 en 2 aan de L2Host kant. Deze verwijzen naar het havenkanaal dat respectievelijk naar de "a"-kant en de "b"-kant gaat.

### <a name="vlans"></a>VLAN's
De netwerkarchitectuur gebruikt VLAN's om servers samen te groeperen in één broadcastdomein. VLAN-nummers voldoen aan de norm 802.1q, die VLAN's nummer 1-4094 ondersteunt.

### <a name="customer-vlans"></a>Klant VLAN's
U beschikt over verschillende VLAN-implementatieopties die u via de Azure-portal implementeren om te voldoen aan de scheidings- en architectuurbehoeften van uw oplossing. U implementeert deze oplossingen via virtuele machines. Zie [Azure-referentiearchitecturen](https://docs.microsoft.com/azure/architecture/reference-architectures/)voor voorbeelden van klantreferentiearchitecturen .

### <a name="edge-architecture"></a>Randarchitectuur
Azure-datacenters zijn gebouwd op zeer redundante en goed ingerichte netwerkinfrastructuren. Microsoft implementeert netwerken binnen de Azure-datacenters met 'need plus one' (N+1) redundantiearchitecturen of beter. Volledige failoverfuncties binnen en tussen datacenters zorgen voor de beschikbaarheid van het netwerk en de service. Extern worden datacenters bediend door speciale netwerkcircuits met hoge bandbreedte. Deze circuits verbinden redundant eigenschappen met meer dan 1200 internetserviceproviders wereldwijd op meerdere peeringpunten. Dit biedt meer dan 2.000 Gbps potentiële randcapaciteit in het hele netwerk.

Filterrouters aan de rand en toegangslaag van het Azure-netwerk bieden gevestigde beveiliging op pakketniveau en helpen ongeautoriseerde pogingen om verbinding te maken met Azure te voorkomen. De routers helpen ervoor te zorgen dat de werkelijke inhoud van de pakketten gegevens in de verwachte indeling bevat en voldoet aan het verwachte communicatieschema voor client/server. Azure implementeert een gelaagde architectuur, bestaande uit de volgende componenten voor netwerksegregatie en toegangscontrole:

- **Edge-routers.** Deze scheiden de applicatie-omgeving van het internet. Edge-routers zijn ontworpen om anti-spoofbescherming te bieden en de toegang te beperken met behulp van ACL's.
- **Distributie (toegangs)routers.** Hiermee kunnen alleen door Microsoft goedgekeurde IP-adressen worden goedgekeurd, anti-spoofing worden verstrekt en verbindingen worden gemaakt met behulp van ACL's.

### <a name="ddos-mitigation"></a>DDOS mitigatie
Distributed denial of service (DDoS) aanvallen blijven een echte bedreiging vormen voor de betrouwbaarheid van online services. Naarmate aanvallen meer gericht en geavanceerd worden, en naarmate de diensten die Microsoft biedt geografisch diverser worden, is het identificeren en minimaliseren van de impact van deze aanvallen een hoge prioriteit.

[Azure DDoS Protection Standard](../../virtual-network/ddos-protection-overview.md) biedt bescherming tegen DDoS-aanvallen. Zie [Azure DDoS Protection: aanbevolen procedures en referentiearchitecturen](ddos-best-practices.md) voor meer informatie.

> [!NOTE]
> Microsoft biedt standaard DDoS-bescherming voor alle Azure-klanten.
>
>

## <a name="network-connection-rules"></a>Regels voor netwerkverbindingen
In het netwerk implementeert Azure edge-routers die beveiliging bieden op pakketniveau om ongeautoriseerde pogingen om verbinding te maken met Azure te voorkomen. Edge-routers zorgen ervoor dat de werkelijke inhoud van de pakketten gegevens in de verwachte indeling bevat en voldoen aan het verwachte communicatieschema voor client/server.

Edge-routers scheiden de toepassingsomgeving van het internet. Deze routers zijn ontworpen om anti-spoofbescherming te bieden en de toegang te beperken met acl.. Microsoft configureert edge routers met behulp van een gelaagde ACL-benadering, om netwerkprotocollen te beperken die de edge routers en access routers mogen doorleiden.

Microsoft positioneert netwerkapparaten op toegangs- en randlocaties om te fungeren als grenspunten waar invallen- of uitgangsfilters worden toegepast.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over wat Microsoft doet om de Azure-infrastructuur te beveiligen:

- [Azure-faciliteiten, lokalen en fysieke beveiliging](physical-security.md)
- [Beschikbaarheid azure-infrastructuur](infrastructure-availability.md)
- [Onderdelen en grenzen van azure-informatiesysteem](infrastructure-components.md)
- [Azure-productienetwerk](production-network.md)
- [Beveiligingsfuncties van Azure SQL Database](infrastructure-sql.md)
- [Azure-productiebewerkingen en -beheer](infrastructure-operations.md)
- [Azure-infrastructuurbewaking](infrastructure-monitoring.md)
- [Integriteit van Azure-infrastructuur](infrastructure-integrity.md)
- [Azure-klantgegevensbeveiliging](protection-customer-data.md)


