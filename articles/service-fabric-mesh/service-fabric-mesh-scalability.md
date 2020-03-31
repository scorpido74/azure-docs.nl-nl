---
title: Schaalbaarheid van Azure Service Fabric Mesh-apps
description: Een van de voordelen van het implementeren van applicaties naar Service Fabric Mesh is de mogelijkheid om uw services eenvoudig te schalen, handmatig of met beleid voor automatisch schalen.
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.openlocfilehash: 474eda904df653d514fd2ee59fa046f1f87a66aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259173"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Mesh-toepassingen voor servicefabric

Een van de belangrijkste voordelen van het implementeren van applicaties naar Service Fabric Mesh is de mogelijkheid voor u om uw services eenvoudig in of uit te schalen. Dit moet worden gebruikt voor het verwerken van verschillende hoeveelheden belasting op uw services of het verbeteren van de beschikbaarheid. U uw services handmatig schalen in of uit- of automatisch schalen.

## <a name="manual-scaling-instances"></a>Exemplaren voor handmatig schalen

In de implementatiesjabloon voor de toepassingsresource heeft elke service een eigenschap *replicaCount* die kan worden gebruikt om in te stellen hoe vaak die service wordt geïmplementeerd. Een toepassing kan bestaan uit meerdere services, waarbij elke service een unieke waarde voor *replicaCount* heeft, die samen worden geïmplementeerd en beheerd. Om het aantal service-replica's te schalen, wijzigt u de waarde *replicaCount* voor elke service die u wilt schalen in de implementatiesjabloon of het parametersbestand. Vervolgens voert u een upgrade van de toepassing uit.

[Zie](service-fabric-mesh-tutorial-template-scale-services.md)Instanties voor het handmatig schalen van services voor het handmatig schalen van services.

## <a name="autoscaling-service-instances"></a>Service-exemplaren automatisch schalen
Automatisch schalen is een extra mogelijkheid van Service Fabric om het aantal service-exemplaren dynamisch te schalen (horizontale schaling). Automatisch schalen geeft een grote elasticiteit en maakt het mogelijk om service-exemplaren in te richten of te verwijderen op basis van CPU- of geheugengebruik.  Met automatisch schalen u het juiste aantal service-exemplaren voor uw werkbelasting uitvoeren en optimaliseren voor kosten.

Per service in het servicebronbestand wordt een beleid voor automatisch schalen gedefinieerd. Elk schaalbeleid bestaat uit twee delen:

- Er wordt een scaling trigger uitgevoerd, die beschrijft wanneer de service wordt geschaald. Er zijn drie factoren die bepalen wanneer de service wordt geschaald. *Lagere belastingsdrempel* is een waarde die bepaalt wanneer de service wordt ingeschaald. Als de gemiddelde belasting van alle exemplaren van de partities lager is dan deze waarde, wordt de service ingeschaald. *Bovenste belastingsdrempel* is een waarde die bepaalt wanneer de service wordt uitgeschaald. Als de gemiddelde belasting van alle exemplaren van de partitie hoger is dan deze waarde, wordt de service uitgeschaald. *Het schaalinterval* bepaalt hoe vaak (in seconden) de trigger wordt gecontroleerd. Zodra de trigger is gecontroleerd, als schalen nodig is, wordt het mechanisme toegepast. Als schalen niet nodig is, wordt er geen actie ondernomen. In beide gevallen wordt trigger niet opnieuw gecontroleerd voordat het schaalinterval verloopt.

- Een schalingsmechanisme, dat beschrijft hoe schalen wordt uitgevoerd wanneer het wordt geactiveerd. *Schaalverhoging* bepaalt hoeveel exemplaren worden toegevoegd of verwijderd wanneer het mechanisme wordt geactiveerd. *Het maximumaantal instance* definieert de bovengrens voor schalen. Als het aantal exemplaren deze limiet bereikt, wordt de service niet uitgeschaald, ongeacht de belasting. *Minimumaantal instantiedefinieert* de ondergrens voor schalen. Als het aantal exemplaren van de partitie deze limiet bereikt, wordt de service niet geschaald, ongeacht de belasting.

Lees [autoscaleservices](service-fabric-mesh-howto-auto-scale-services.md)voor meer informatie over het instellen van een autoscale-beleid voor uw service.

## <a name="next-steps"></a>Volgende stappen

Zie Bronnen voor [servicefabric](service-fabric-mesh-service-fabric-resources.md)
