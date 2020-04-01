---
title: Overzicht van poorten met hoge beschikbaarheid in Azure
titleSuffix: Azure Load Balancer
description: Meer informatie over de taakverdeling van poorten met hoge beschikbaarheid op een interne load balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2019
ms.author: allensu
ms.openlocfilehash: d3bd1156de4aed7d1ea5c530605697f2dc80d63c
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476974"
---
# <a name="high-availability-ports-overview"></a>Overzicht van poorten met hoge beschikbaarheid

Azure Standard Load Balancer helpt u tcp- en UDP-stromen op alle poorten tegelijk te laden wanneer u een interne load balancer gebruikt. 

Een ha-balanceringsregel (high availability ports) is een variant van een load-balancing regel, geconfigureerd op een interne Standard Load Balancer. U het gebruik van een load balancer vereenvoudigen door één regel op te geven voor het in evenwicht brengen van alle TCP- en UDP-stromen die op alle poorten van een interne standaardloadbalanskomen. De load-balancing beslissing wordt gemaakt per flow. Deze actie is gebaseerd op de volgende 5-tuple-verbinding: bron-IP-adres, bronpoort, bestemmings-IP-adres, doelpoort en protocol

De ha-poorten load-balancing regels helpen u met kritieke scenario's, zoals hoge beschikbaarheid en schaal voor netwerk virtuele apparaten (NVA's) binnen virtuele netwerken. De functie kan ook helpen wanneer een groot aantal poorten in balans moet zijn. 

De load-balancing rules van de HA-poorten worden geconfigureerd wanneer u de front-end- en back-endpoorten instelt op **0** en het protocol op **Alles.** De interne load balancer-bron balanceert vervolgens alle TCP- en UDP-stromen, ongeacht het poortnummer

## <a name="why-use-ha-ports"></a>Waarom HA-poorten gebruiken?

### <a name="network-virtual-appliances"></a><a name="nva"></a>Netwerk virtuele apparaten

U NVA's gebruiken om uw Azure-workload te beveiligen tegen meerdere soorten beveiligingsbedreigingen. Wanneer u NVA's in deze scenario's gebruikt, moeten ze betrouwbaar en zeer beschikbaar zijn en moeten ze uitschalen naar vraag.

U deze doelen eenvoudig bereiken door NVA-exemplaren toe te voegen aan de back-endpool van uw interne load balancer en een HA-poortload-balancerregel te configureren.

Voor NVA HA-scenario's bieden HA-poorten de volgende voordelen:
- Snelle failover bieden aan gezonde instanties, met statussondes per instantie
- Zorgen voor hogere prestaties met scale-out naar *n-actieve*exemplaren
- *N-actieve*en actief-passieve scenario's bieden
- Elimineer de noodzaak van complexe oplossingen, zoals Apache ZooKeeper-knooppunten voor het bewaken van apparaten

Het volgende diagram bevat een hub-and-spoke virtuele netwerkimplementatie. De spaken dwingen hun verkeer naar de hub virtuele netwerk en via de NVA, alvorens de vertrouwde ruimte te verlaten. De NVA's zitten achter een interne Standard Load Balancer met een HA-poortconfiguratie. Al het verkeer kan dienovereenkomstig worden verwerkt en doorgestuurd. Wanneer deze is geconfigureerd zoals weergegeven in het volgende diagram, biedt een load-balancingregel van HA-poorten bovendien stroomsymmetrie voor binnendringend en uitgangsverkeer.

<a node="diagram"></a>
![Diagram van hub-and-spoke virtueel netwerk, met NVA's geïmplementeerd in HA-modus](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Als u NVA's gebruikt, bevestigt u met hun providers hoe u HA-poorten het beste gebruiken en om te weten welke scenario's worden ondersteund.

### <a name="load-balancing-large-numbers-of-ports"></a>Load-balancing grote aantallen poorten

U HA-poorten ook gebruiken voor toepassingen waarvoor een groot aantal poorten moeten worden belast. U deze scenario's vereenvoudigen met behulp van een interne [Standaard Load Balancer](load-balancer-standard-overview.md) met HA-poorten. Eén regel voor het balanceren van de regel vervangt meerdere afzonderlijke regels voor het balanceren van de lastenafweging, één voor elke poort.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

De ha-poortenfunctie is beschikbaar in alle algemene Azure-regio's.

## <a name="supported-configurations"></a>Ondersteunde configuraties

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Een enkele, niet-zwevende IP-configuratie (niet-Direct Server Return) HA-poorten op een interne standaardloadbalancer

Deze configuratie is een basis-HA-poorten configuratie. U een load-balancing rule van HA-poorten configureren op één front-end IP-adres door het volgende te doen:
1. Schakel tijdens het configureren van Standard Load Balancer het selectievakje **HA-poorten** in de regelconfiguratie Load Balancer in.
2. Selecteer **Uitgeschakeld**voor **zwevend IP**.

Deze configuratie staat geen andere configuratie van de regel voor het balanceren van de lastenverdeling toe op de huidige load balancer-bron. Het maakt ook geen andere interne load balancer resource configuratie voor de gegeven set van back-end exemplaren.

U echter een openbare standaardloadbalancer configureren voor de back-endinstanties naast deze HA-poortenregel.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Een enkele, zwevende IP-configuratie (Direct Server Return) HA-poorten op een interne standaardloadbalancer

U uw load balancer ook configureren om een load-balancing rule te gebruiken met **HA-poort** met één front-end door het **zwevende IP** in te stellen op **Ingeschakeld.** 

Door deze configuratie te gebruiken, u meer zwevende IP-regels voor het balanceren van de lastenen en/of een openbare load balancer toevoegen. U echter geen gebruik maken van een niet-zwevende IP, HA-poorten load-balancing configuratie op de top van deze configuratie.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Meerdere HA-poorten configuraties op een interne Standard Load Balancer

Als voor uw scenario meer dan één HA-poortfrontend moet worden geconfigureerd voor dezelfde back-endpool, u het volgende doen: 
- Configureer meer dan één front-end privé-IP-adres voor één interne StandaardloadBalancer-bron.
- Configureer meerdere regels voor het balanceren van de taak, waarbij met elke regel één uniek front-end IP-adres is geselecteerd.
- Selecteer de optie **HA-poorten** en stel **Zwevend IP** in **op Ingeschakeld** voor alle regels voor het balanceren van de lasten.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Een interne load balancer met HA-poorten en een public load balancer op dezelfde back-end instantie

U *één* openbare standaardloadbalancerbron configureren voor de back-endresources, samen met één interne standaardloadbalansr met HA-poorten.

>[!NOTE]
>Deze mogelijkheid is momenteel beschikbaar via Azure Resource Manager-sjablonen, maar is niet beschikbaar via de Azure-portal.

## <a name="limitations"></a>Beperkingen

- Ha-poorten load-balancing regels zijn alleen beschikbaar voor interne Standard Load Balancer.
- Het combineren van een HA-poorten load-balancing regel en een niet-HA poorten load-balancing regel die naar dezelfde backend ipconfigurations wordt ondersteund.
- Bestaande IP-fragmenten worden doorgestuurd door HA-poorten load-balancing regels naar dezelfde bestemming als eerste pakket.  IP-fragmentatie van een UDP- of TCP-pakket wordt niet ondersteund.
- Flowsymmetrie (voornamelijk voor NVA-scenario's) wordt alleen ondersteund met backend-instantie en één NIC (en enkele IP-configuratie) wanneer deze worden gebruikt zoals in het bovenstaande diagram en met ha-poorten load-balancing-regels. Het is niet voorzien in een ander scenario. Dit betekent dat twee of meer Load Balancer-resources en hun respectieve regels onafhankelijke beslissingen nemen en nooit worden gecoördineerd. Zie de beschrijving en het diagram voor [virtuele netwerkapparaten.](#nva) Wanneer u meerdere NIC's gebruikt of de NVA insandwicht tussen een openbare en interne load balancer, is flowsymmetrie niet beschikbaar.  Mogelijk u dit omzeilen door de instroom naar het IP-apparaat te achterhalen door nat'ing te gebruiken om antwoorden op dezelfde NVA te laten aankomen.  We raden echter ten zeerste aan om één NIC te gebruiken en de referentiearchitectuur in het bovenstaande diagram te gebruiken.


## <a name="next-steps"></a>Volgende stappen

- [HA-poorten configureren op een interne standaardloadbalansr](load-balancer-configure-ha-ports.md)
- [Meer informatie over Standard Load Balancer](load-balancer-standard-overview.md)
