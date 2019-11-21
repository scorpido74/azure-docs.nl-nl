---
title: Overzicht van poorten voor hoge beschikbaarheid in Azure
titleSuffix: Azure Load Balancer
description: Meer informatie over hoge beschikbaarheid poorten taakverdeling op een interne load balancer.
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
ms.openlocfilehash: c6529e2585a7fca2d160d093d303afa02e6f9379
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215080"
---
# <a name="high-availability-ports-overview"></a>Overzicht van poorten voor hoge beschikbaarheid

Azure Standard Load Balancer kunt u taakverdeling TCP en UDP-stromen op alle poorten tegelijkertijd wanneer u een interne load balancer. 

Een regel voor Load Balancing met hoge Beschik baarheid (HA) is een variant van een taakverdelings regel, geconfigureerd op een interne Standard Load Balancer. U kunt het gebruik van een load balancer vereenvoudigen door te geven van een regel voor het verdelen van alle TCP en UDP-stromen die op alle poorten van een interne standaardversie van Load Balancer binnenkomen. De beslissing load balancing is per stroom gemaakt. Deze actie is gebaseerd op de volgende vijf-tuple-verbinding: bron-IP-adres, bron poort, doel-IP-adres, doel poort en protocol

De taakverdelings regels voor de HA-poorten helpen u bij kritieke scenario's, zoals hoge Beschik baarheid en schaal voor Nva's (Network Virtual Appliance) in virtuele netwerken. De functie kan ook helpen bij een groot aantal poorten verdeeld worden moet. 

De taakverdelings regels voor de HA-poorten worden geconfigureerd wanneer u de front-end-en back-end-poorten instelt op **0** en het protocol voor **alle**. Met de interne load balancer resource worden vervolgens alle TCP-en UDP-stromen gebalanceerd, ongeacht het poort nummer

## <a name="why-use-ha-ports"></a>Waarom HA-poorten gebruiken?

### <a name="nva"></a>Virtuele netwerkapparaten

U kunt NVA's gebruiken om u te helpen beveiligen uw Azure-workload tegen meerdere soorten beveiligingsrisico's. Wanneer u de NVA's in deze scenario's gebruikt, moeten ze betrouwbaar en maximaal beschikbare en ze moeten uitbreiden voor de vraag.

U kunt deze doelen bereiken door eenvoudig NVA exemplaren toe te voegen aan de back-endpool van de interne load balancer en load balancer-regel voor het configureren van een HA-poorten.

Voor NVA HA scenario's bieden HA-poorten aan de volgende voordelen:
- Snelle failover in orde-exemplaren voorzien van statuscontroles per exemplaar
- Hogere prestaties met scale-out aan *n*-actieve exemplaren
- Geef *n*-actieve en actief-passief-scenario's
- Voorkom de noodzaak voor complexe oplossingen, zoals Apache ZooKeeper-knooppunten voor het bewaken van apparaten

Het volgende diagram toont een virtueel netwerk hub en spoke-implementatie. Geforceerde tunneling in de knooppunten hun verkeer naar de virtuele hub-netwerk en via de NVA, voordat u de vertrouwde ruimte. De NVA's zich achter een interne Standard Load Balancer met de configuratie van een HA-poorten. Al het verkeer kan worden verwerkt en dienovereenkomstig doorgestuurd. Wanneer de configuratie wordt weer gegeven in het volgende diagram, biedt de taak verdelings regel voor HA-poorten daarnaast een stroom symmetrie voor binnenkomend en uitgaand verkeer.

<a node="diagram"></a>
![diagram van een hub-en-spoke-virtueel netwerk, waarbij Nva's is geïmplementeerd in de HA-modus](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Als u met behulp van NVA's, controleert u of met hun leveranciers van het best gebruik HA-poorten en voor meer informatie over welke scenario's worden ondersteund.

### <a name="load-balancing-large-numbers-of-ports"></a>Load balancing groot aantal poorten

U kunt ook de HA-poorten gebruiken voor toepassingen waarvoor de taakverdeling van een groot aantal poorten. U kunt deze scenario's vereenvoudigen met behulp van een interne [Standard Load Balancer](load-balancer-standard-overview.md) met HA-poorten. Een enkele load balancing-regel vervangt meerdere afzonderlijke-taakverdelingsregels, één voor elke poort.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

De HA-poorten-functie is beschikbaar in alle globale Azure-regio's.

## <a name="supported-configurations"></a>Ondersteunde configuraties

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Een configuratie met één en niet-zwevend IP (niet - Direct Server Return) HA-poorten op een interne Standard Load Balancer

Deze configuratie is een basisconfiguratie van de HA-poorten. U kunt configureren dat een HA poorten taakverdeling regel op één front-end-IP-adres door het volgende te doen:
1. Tijdens het configureren van Standard Load Balancer, selecteer de **HA-poorten** selectievakje in de configuratie van de Load Balancer-regel.
2. Voor **zwevend IP**, selecteer **uitgeschakelde**.

Deze configuratie is niet toegestaan voor een andere load balancing regelconfiguratie op de huidige load balancer-resource. Daarnaast kunt u geen andere interne load balancer-resource-configuratie voor de gegeven reeks back-end-exemplaren.

U kunt echter een openbare Standard Load Balancer voor de back-endexemplaren naast deze regel HA-poorten configureren.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Een configuratie met één en zwevend IP (Direct Server Return) HA-poorten op een interne Standard Load Balancer

Op deze manier kunt u de load balancer voor het gebruik van een regel voor taakverdeling met configureren **HA poort** met een één front-end door in te stellen de **zwevend IP** naar **ingeschakeld**. 

U kunt meer zwevende IP-taakverdelingsregels en/of een openbare load balancer toevoegen met behulp van deze configuratie. U kunt geen echter een niet-zwevend IP-adres, HA-poorten gebruiken load balancing-configuratie op deze configuratie.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Configuraties met meerdere HA-poorten op een interne Standard Load Balancer

Als uw scenario vereist dat u meer dan één HA-poort-front-end voor de dezelfde back-end-pool configureren, kunt u het volgende doen: 
- Meer dan één front-end privé IP-adres configureren voor een enkele interne Standard Load Balancer-resource.
- Configureer meerdere load balancing regels, waarbij elke regel één unieke front-end-IP-adres geselecteerd heeft.
- Selecteer de **HA-poorten** optie en stel vervolgens **zwevend IP** naar **ingeschakeld** voor alle load balancing-regels.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Een interne load balancer met HA-poorten en een openbare load balancer op hetzelfde exemplaar van de back-end

U kunt configureren *één* openbare Standard Load Balancer-resource voor de back-end-resources, samen met een enkele interne Standard Load Balancer met HA-poorten.

>[!NOTE]
>Deze mogelijkheid is momenteel beschikbaar zijn via Azure Resource Manager-sjablonen, maar het is niet beschikbaar via de Azure-portal.

## <a name="limitations"></a>Beperkingen

- De taakverdelings regels voor HA-poorten zijn alleen beschikbaar voor interne Standard Load Balancer.
- De combinatie van een HA-poorten taakverdelingsregel en een niet-HA-poorten taakverdelingsregel wordt niet ondersteund.
- Bestaande IP-fragmenten worden doorgestuurd door HA-poorten taakverdelings regels naar hetzelfde doel als eerste pakket.  IP-fragmentatie van een UDP-of TCP-pakket wordt niet ondersteund.
- De taakverdelings regels voor de HA-poorten zijn niet beschikbaar voor IPv6.
- Flow-symmetrie (voornamelijk voor NVA-scenario's) wordt ondersteund met een back-end-exemplaar en één NIC (en één IP-configuratie) alleen wanneer deze worden gebruikt zoals weer gegeven in het diagram hierboven en met behulp van de taakverdelings regels voor HA-poorten. Het is in geen enkel ander scenario opgenomen. Dit betekent dat twee of meer Load Balancer resources en de bijbehorende regels onafhankelijke beslissingen nemen en nooit gecoördineerd zijn. Zie de beschrijving en diagram voor [virtuele netwerkapparaten](#nva). Wanneer u meerdere Nic's gebruikt of de NVA tussen een open bare en interne Load Balancer, is de stroom symmetrie niet beschikbaar.  U kunt dit mogelijk omzeilen door middel van de bron NAT'ing de ingangs stroom naar het IP-adres van het apparaat zodat antwoorden op dezelfde NVA kunnen worden ontvangen.  We raden u echter ten zeerste aan om één NIC te gebruiken en de referentie architectuur te gebruiken die wordt weer gegeven in het bovenstaande diagram.


## <a name="next-steps"></a>Volgende stappen

- [HA-poorten op een interne Standard Load Balancer configureren](load-balancer-configure-ha-ports.md)
- [Meer informatie over de standaardversie van Load Balancer](load-balancer-standard-overview.md)
