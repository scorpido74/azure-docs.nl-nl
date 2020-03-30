---
title: Herconfiguratie in Azure Service Fabric
description: Meer informatie over configuraties voor stateful servicereplica's en het proces van het opnieuw configureren van Service Fabric wordt gebruikt om consistentie en beschikbaarheid tijdens de wijziging te behouden.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: bd46a7776495624affef77a44fcf68334750ba17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609992"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Herconfiguratie in Azure Service Fabric
Een *configuratie* wordt gedefinieerd als de replica's en hun rollen voor een partitie van een stateful service.

Een *herconfiguratie* is het proces van het verplaatsen van de ene configuratie naar een andere configuratie. Het maakt een wijziging in de replica set voor een partitie van een stateful service. De oude configuratie wordt de *vorige configuratie (PC)* genoemd en de nieuwe configuratie wordt de *huidige configuratie (CC)* genoemd. Het configuratieprotocol in Azure Service Fabric behoudt de consistentie en behoudt de beschikbaarheid tijdens wijzigingen in de replicaset.

Failover Manager initieert herconfiguraties in reactie op verschillende gebeurtenissen in het systeem. Als de primaire bijvoorbeeld mislukt, wordt een herconfiguratie gestart om een actieve secundaire met een primaire te promoten. Een ander voorbeeld is een reactie op toepassingsupgrades wanneer het nodig kan zijn om het primaire naar een ander knooppunt te verplaatsen om het knooppunt te upgraden.

## <a name="reconfiguration-types"></a>Configuratietypen opnieuw configureren
Herconfiguraties kunnen in twee typen worden ingedeeld:

- Herconfiguraties waarbij de primaire wijziging is:
    - **Failover:** Failovers zijn herconfiguraties als reactie op het mislukken van een primaire uitvoering.
    - **SwapPrimary**: Swaps zijn herconfiguraties waarbij Service Fabric een draaiende primaire versie van het ene knooppunt naar het andere moet verplaatsen, meestal als reactie op load balancing of een upgrade.

- Herconfiguraties waarbij de primaire niet wordt gewijzigd.

## <a name="reconfiguration-phases"></a>Herconfiguratiefasen
Een herconfiguratie verloopt in verschillende fasen:

- **Fase0**: Deze fase vindt plaats in swapprimaire herconfiguraties waarbij de huidige primaire status wordt overgedragen naar de nieuwe primaire en overgangen naar actieve secundaire.

- **Fase1**: Deze fase vindt plaats tijdens herconfiguraties waarbij de primaire verandert. Tijdens deze fase identificeert Service Fabric de juiste primaire onder de huidige replica's. Deze fase is niet nodig tijdens swap-primaire herconfiguraties omdat de nieuwe primaire al is gekozen. 

- **Fase2**: Tijdens deze fase zorgt Service Fabric ervoor dat alle gegevens beschikbaar zijn in een meerderheid van de replica's van de huidige configuratie.

Er zijn verschillende andere fasen die alleen voor intern gebruik zijn.

## <a name="stuck-reconfigurations"></a>Vastgeplakte herconfiguraties
Herconfiguraties kunnen om verschillende redenen vast komen te *zitten.* Enkele van de gemeenschappelijke redenen zijn:

- **Down replica's**: Sommige herconfiguratiefasen vereisen dat een meerderheid van de replica's in de configuratie omhoog moet zijn.
- **Netwerk- of communicatieproblemen**: Herconfiguraties vereisen netwerkconnectiviteit tussen verschillende knooppunten.
- **API-fouten**: Het herconfiguratieprotocol vereist dat service-implementaties bepaalde API's voltooien. Als u het annuleringstoken bijvoorbeeld niet in een betrouwbare service eert, zorgt swapprimary voor herconfiguraties om vast te lopen.

Gebruik statusrapporten van systeemcomponenten, zoals System.FM, System.RA en System.RAP, om te diagnosticeren waar een herconfiguratie vastzit. De [pagina met systeemstatusrapporten](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) beschrijft deze gezondheidsrapporten.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over servicefabric-concepten:

- [Levenscyclus van Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)
- [Systeemstatusrapporten](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Replica's en exemplaren](service-fabric-concepts-replica-lifecycle.md)
