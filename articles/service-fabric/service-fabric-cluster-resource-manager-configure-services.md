---
title: Statistieken en plaatsingsinstellingen opgeven
description: Meer informatie over het beschrijven van een Service Fabric-service door statistieken, plaatsingsbeperkingen en ander plaatsingsbeleid op te geven.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d4dcd319000edb204ba188ed14b4c797dba5cd38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610094"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Instellingen voor clusterbronbeheer configureren voor Service Fabric-services
Met de Cluster resourcemanager servicestructuur u de regels die gelden voor elke met de naam genoemde service van het individu, fijnmazige controle beheren. Elke benoemde service kan regels opgeven voor de manier waarop deze in het cluster moet worden toegewezen. Elke benoemde service kan ook de set statistieken definiëren die deze wil rapporteren, inclusief hoe belangrijk ze zijn voor die service. Het configureren van services wordt opgesplitst in drie verschillende taken:

1. Plaatsingsbeperkingen configureren
2. Statistieken configureren
3. Geavanceerd plaatsingsbeleid en andere regels configureren (minder gebruikelijk)

## <a name="placement-constraints"></a>Plaatsingsbeperkingen
Plaatsingsbeperkingen worden gebruikt om te bepalen op welke knooppunten in het cluster een service daadwerkelijk kan worden uitgevoerd. Typisch een bepaalde benoemde serviceinstantie of alle services van een bepaald type die zijn beperkt om op een bepaald type knooppunt uit te voeren. Plaatsingsbeperkingen zijn uitbreidbaar. U elke set eigenschappen per knooppunttype definiëren en vervolgens voor deze eigenschappen selecteren met beperkingen bij het maken van services. U ook de plaatsingsbeperkingen van een service wijzigen terwijl deze wordt uitgevoerd. Hiermee u reageren op wijzigingen in het cluster of de vereisten van de service. De eigenschappen van een bepaald knooppunt kunnen ook dynamisch worden bijgewerkt in het cluster. Meer informatie over plaatsingsbeperkingen en hoe u deze configureren, vindt u in [dit artikel](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Metrische gegevens
Statistieken zijn de set resources die een bepaalde benoemde service nodig heeft. De metrische configuratie van een service bevat hoeveel van die resource elke stateful replica of stateless instantie van die service standaard verbruikt. Statistieken bevatten ook een gewicht dat aangeeft hoe belangrijk het in evenwicht brengen van die statistiek is voor die service, voor het geval afwegingen nodig zijn.

## <a name="advanced-placement-rules"></a>Geavanceerde plaatsingsregels
Er zijn andere typen plaatsingsregels die nuttig zijn in minder voorkomende scenario's. Een aantal voorbeelden:
- Beperkingen die helpen bij geografisch gedistribueerde clusters
- Bepaalde toepassingsarchitecturen

Andere plaatsingsregels worden geconfigureerd via correlaties of beleidsregels.

## <a name="next-steps"></a>Volgende stappen
- Statistieken zijn hoe de Clusterresourcemanger van de servicestructuur het verbruik en de capaciteit in het cluster beheert. Bekijk [dit artikel](service-fabric-cluster-resource-manager-metrics.md) voor meer informatie over statistieken en hoe u deze configureren
- Affiniteit is een modus die u configureren voor uw services. Het is niet gebruikelijk, maar als je het nodig hebt kun je leren over het [hier](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Er zijn veel verschillende plaatsingsregels die op uw service kunnen worden geconfigureerd om extra scenario's af te handelen. U meer informatie vinden over die verschillende plaatsingsbeleid [hier](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Begin vanaf het begin en [krijg een inleiding tot de Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Als u wilt weten hoe de clusterresourcemanager de belasting in het cluster beheert en in evenwicht brengt, raadpleegt u het artikel over [het balanceren van de belasting](service-fabric-cluster-resource-manager-balancing.md)
- De Clusterresourcemanager heeft veel opties voor het beschrijven van het cluster. Voor meer informatie over hen, check out dit artikel over [het beschrijven van een Service Fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md)
