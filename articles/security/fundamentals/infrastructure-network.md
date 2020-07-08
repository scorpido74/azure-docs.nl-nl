---
title: Azure-netwerk architectuur
description: Dit artikel bevat een algemene beschrijving van het Microsoft Azure infrastructuur netwerk.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "68727177"
---
# <a name="azure-network-architecture"></a>Azure-netwerk architectuur
De Azure-netwerk architectuur volgt een gewijzigde versie van het model Standard/Distribution/Access van de branche, met afzonderlijke hardwareapparaten. De lagen zijn onder andere:

- Core (Data Center-routers)
- Distributie (toegangs routers en L2-aggregatie). De distributie-laag scheidt N3-route ring van L2-switches.
- Toegang (L2-hostservice)

De netwerk architectuur heeft twee niveaus van laag 2-switches. Met één laag wordt het verkeer geaggregeerd vanuit de andere laag. De tweede laag is een lus voor het opnemen van redundantie. De architectuur biedt een flexibeler VLAN-footprint en verbetert de schaal baarheid van poorten. De architectuur houdt een DISTINCT van L2 en L3, waardoor het gebruik van hardware in elk van de afzonderlijke lagen in het netwerk wordt geminimaliseerd, en het minimaliseren van de fout in één laag van invloed is op een of meer lagen. Het gebruik van trunks maakt het delen van resources mogelijk, zoals de connectiviteit met de L3-infra structuur.

## <a name="network-configuration"></a>Netwerkconfiguratie
De netwerk architectuur van een Azure-cluster binnen een Data Center bestaat uit de volgende apparaten:

- Routers (Data Center, toegangs router en Border blad routers)
- Switches (aggregatie en top-of-rack-switches)
- Digi CMs
- Energie distributie-eenheden

Azure heeft twee afzonderlijke architecturen. Sommige bestaande Azure-klanten en gedeelde services bevinden zich op de standaard LAN-architectuur (DLA), terwijl nieuwe regio's en virtuele klanten zich op een Quantum 10-architectuur (Q10) bevinden. De DLA-architectuur is een traditioneel structuur ontwerp, met actieve/passieve toegangs routers en Acl's (Security Access Control Lists) die worden toegepast op de toegangs routers. De Quantum 10-architectuur is een sluit/mesh ontwerp van routers, waarbij Acl's niet worden toegepast op de routers. In plaats daarvan worden Acl's onder de route ring toegepast, via software Load Balancing (SLB) of door software gedefinieerde VLAN'S.

In het volgende diagram vindt u een overzicht van de netwerk architectuur in een Azure-cluster:

![Diagram van Azure-netwerk](./media/infrastructure-network/network-arch.png)

### <a name="quantum-10-devices"></a>Quantum 10-apparaten
Het ontwerp van de Quantum 10 voert de switch van laag 3 uit op meerdere apparaten in een Clos/mesh-ontwerp. De voor delen van het ontwerp van Q10 bevatten een grotere mogelijkheid en een grotere mogelijkheid om bestaande netwerk infrastructuur te schalen. Het ontwerp maakt gebruik van Border-blad routers, spin-switches en top-of-rack-routers voor het door geven van verkeer aan clusters in meerdere routes, waardoor fout tolerantie mogelijk is. Software-taak verdeling, in plaats van hardwareapparaten, verwerkt beveiligings Services, zoals Network Address Translation.

### <a name="access-routers"></a>Toegangs routers
De distributie/toegang tot L3-routers (ARs) voeren de primaire routerings functionaliteit uit voor de distributie-en toegangs lagen. Deze apparaten worden geïmplementeerd als een paar en zijn de standaard gateway voor subnetten. Elk AR-paar kan meerdere N2-aggregatie switch paren ondersteunen, afhankelijk van de capaciteit. Het maximum aantal is afhankelijk van de capaciteit van het apparaat en de fout domeinen. Een typisch aantal is drie MB aggregatie switch paren per AR-paar.

### <a name="l2-aggregation-switches"></a>L2-aggregatie switches  
Deze apparaten fungeren als een aggregatie punt voor L2-verkeer. Ze zijn de distributie-laag voor de L2-infra structuur en kunnen grote hoeveel heden verkeer verwerken. Omdat deze apparaten verkeer verzamelen, zijn er 802.1 q-functies en technologieën met een hoge band breedte zoals poort aggregatie en 10GE vereist.

### <a name="l2-host-switches"></a>L2-host switches
Hosts maken rechtstreeks verbinding met deze switches. Ze kunnen rack-gekoppelde switches of chassis implementaties zijn. De 802.1 q-standaard staat het aanwijzen van één VLAN toe als systeem eigen VLAN en behandelt dat VLAN als normale (niet-gelabeld) Ethernet-framing. Onder normale omstandigheden worden frames op het systeem eigen VLAN verzonden en worden ze ongecodeerd ontvangen op een 802.1 q-trunk-poort. Deze functie is ontworpen voor migratie naar 802.1 q en compatibiliteit met apparaten die niet voldoen aan het 802.1 q-apparaat. In deze architectuur gebruikt alleen de netwerk infrastructuur het systeem eigen VLAN.

Deze architectuur bevat een standaard voor een systeem eigen VLAN-selectie. De standaard garandeert, waar mogelijk, dat de AR-apparaten een uniek, systeem eigen VLAN hebben voor elke trunk en de L2Aggregation voor L2Aggregation-Trunks. De L2Aggregation-naar-L2Host-switch-Trunks hebben een niet-standaard systeem eigen VLAN.

### <a name="link-aggregation-8023ad"></a>Koppelings aggregatie (802.3 AD)
Koppelings aggregatie maakt het mogelijk meerdere afzonderlijke koppelingen samen te bundelen en te worden behandeld als één logische koppeling. Om operationele fout opsporing te vergemakkelijken, moet het nummer dat wordt gebruikt voor het toewijzen van poort kanaal interfaces, worden gestandaardiseerd. De rest van het netwerk gebruikt hetzelfde nummer aan beide uiteinden van een poort kanaal.

De getallen die zijn opgegeven voor de switch L2Agg naar L2Host, zijn de poort kanaal nummers die worden gebruikt voor de L2Agg-zijde. Omdat het bereik van getallen groter is dan aan de L2Host zijde, is de standaard het gebruik van de nummers 1 en 2 aan de kant van de L2Host. Deze verwijzen naar het poort kanaal, respectievelijk naar de kant a en de b-zijde.

### <a name="vlans"></a>Vlan's
De netwerk architectuur maakt gebruik van VLAN'S voor het groeperen van servers in één broadcast-domein. VLAN-nummers voldoen aan de 802.1 q-standaard, die ondersteuning biedt voor VLAN'S met de nummering 1-4094.

### <a name="customer-vlans"></a>Klanten-VLAN'S
U kunt verschillende VLAN-implementatie opties implementeren via de Azure Portal om te voldoen aan de schei ding en architectuur vereisten van uw oplossing. U implementeert deze oplossingen via virtuele machines. Zie [Azure-referentie architecturen](https://docs.microsoft.com/azure/architecture/reference-architectures/)voor voor beelden van klant referentie architectuur.

### <a name="edge-architecture"></a>Edge-architectuur
Azure-data centers zijn gebouwd op zeer redundante en goed ingerichte netwerk infrastructuur. Micro soft implementeert netwerken binnen de Azure-data centers met ' behoefte plus één ' (N + 1) redundantie architecturen of beter. Volledige failover-functies binnen en tussen data centers helpen de beschik baarheid van netwerken en services te garanderen. Extern, Data Centers worden geleverd met een speciale netwerk circuit met hoge band breedte. Deze circuits verloopt redundante verbinding met eigenschappen met meer dan 1200 Internet serviceproviders globaal op meerdere peering punten. Dit biedt een capaciteit van meer dan 2.000 Gbps aan het netwerk.

Het filteren van routers aan de Edge-en Access-laag van het Azure-netwerk biedt goed opgezette beveiliging op pakket niveau en helpt te voor komen dat niet-geautoriseerde pogingen om verbinding te maken met Azure. De routers helpen ervoor te zorgen dat de daad werkelijke inhoud van de pakketten gegevens in de verwachte indeling bevatten en in overeenstemming is met het verwachte client/server-communicatie schema. Azure implementeert een gelaagde architectuur, die bestaat uit de volgende onderdelen voor netwerk scheiding en-toegangs beheer:

- **Edge-routers.** Deze schei ding de toepassings omgeving van het internet. Edge-routers zijn ontworpen om beveiliging tegen spoofberichten te bieden en toegang te beperken met behulp van Acl's.
- **Distributie-routers (Access).** Hiermee worden alleen door micro soft goedgekeurde IP-adressen toegestaan, wordt anti-spoofing geboden en worden verbindingen tot stand gebracht met behulp van Acl's.

### <a name="ddos-mitigation"></a>DDOS-beperking
DDoS-aanvallen (Distributed Denial of service) blijven een echte bedreiging bieden voor de betrouw baarheid van onlineservices. Als aanvallen zijn gerichter en Geavanceerd geworden, en naarmate de services van micro soft meer geografisch worden, is het identificeren en beperken van de impact van deze aanvallen een hoge prioriteit.

[Azure DDoS Protection Standard](../../virtual-network/ddos-protection-overview.md) biedt beveiliging tegen DDoS-aanvallen. Zie [Azure DDoS Protection: Aanbevolen procedures en referentie architecturen](ddos-best-practices.md) voor meer informatie.

> [!NOTE]
> Micro soft biedt standaard DDoS-beveiliging voor alle Azure-klanten.
>
>

## <a name="network-connection-rules"></a>Regels voor netwerk verbindingen
Op het netwerk implementeert Azure Edge-routers die beveiliging op pakket niveau bieden om te voor komen dat niet-geautoriseerde pogingen om verbinding te maken met Azure. Edge-routers zorgen ervoor dat de daad werkelijke inhoud van de pakketten gegevens in de verwachte indeling bevat en aan het verwachte client/server-communicatie schema voldoet.

Edge-routers scheiden de toepassings omgeving van het internet. Deze routers zijn ontworpen om beveiliging tegen spoofberichten te bieden en de toegang te beperken met behulp van Acl's. Micro soft configureert Edge-routers door gebruik te maken van een gelaagde ACL-benadering, om netwerk protocollen te beperken die de Edge-routers mogen door geven en toegang krijgen tot routers.

Micro soft positioneert netwerk apparaten op toegang en Edge-locaties om te fungeren als grens punten waar ingangs-of uitzonderings filters worden toegepast.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over wat micro soft doet bij het beveiligen van de Azure-infra structuur:

- [Azure-faciliteiten,-locaties en fysieke beveiliging](physical-security.md)
- [Beschik baarheid van Azure-infra structuur](infrastructure-availability.md)
- [Azure Information System-onderdelen en-grenzen](infrastructure-components.md)
- [Productie netwerk van Azure](production-network.md)
- [Azure SQL Database beveiligings functies](infrastructure-sql.md)
- [Azure-productie bewerkingen en-beheer](infrastructure-operations.md)
- [Bewaking van Azure-infra structuur](infrastructure-monitoring.md)
- [Integriteit van Azure-infra structuur](infrastructure-integrity.md)
- [Azure-klant gegevens beveiliging](protection-customer-data.md)


