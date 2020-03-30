---
title: Statusopslagopties op Azure Service Fabric-mesh
description: Meer informatie over het betrouwbaar opslaan van status in Service Fabric Mesh-toepassingen die worden uitgevoerd op Azure Service Fabric Mesh.
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 0b0bd611fa86d155bb5bf2e07808e7365e28871c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259095"
---
# <a name="state-management-with-service-fabric"></a>Staatsbeheer met Service Fabric

Service Fabric ondersteunt veel verschillende opties voor statusopslag. Zie [Service Fabric Concepts: State](/azure/service-fabric/service-fabric-concepts-state)voor een conceptueel overzicht van de state management patronen en Service Fabric. Al deze zelfde concepten zijn van toepassing of uw diensten binnen of buiten Service Fabric Mesh worden uitgevoerd. 

Met Service Fabric Mesh u eenvoudig een nieuwe toepassing implementeren en deze verbinden met een bestaand gegevensarchief dat in Azure wordt gehost. Naast het gebruik van een externe database, zijn er verschillende opties voor het opslaan van gegevens, afhankelijk van of de service lokale of externe opslag wenst. 

## <a name="volumes"></a>Volumes

Containers maken vaak gebruik van tijdelijke schijven. Tijdelijke schijven zijn vluchtig, echter, dus je krijgt een nieuwe tijdelijke schijf en verliest de informatie wanneer een container crasht. Het is ook moeilijk om informatie over tijdelijke schijven te delen met andere containers. Volumes zijn mappen die in uw containerexemplaren worden gemonteerd die u gebruiken om de status aan te houden. Volumes geven u bestandsopslag voor algemene doeleinden en stellen u in staat om bestanden te lezen/schrijven met behulp van normale I/O-bestands-API's op schijf. De bron Volume beschrijft hoe u een map monteren en welke back-upopslag u wilt gebruiken. U Azure File storage of Service Fabric Volume disk kiezen om gegevens op te slaan.

![Volumes][image3]

### <a name="service-fabric-reliable-volume"></a>Betrouwbaar volume van de servicestof

Service Fabric Reliable Volume is een Docker volume driver die wordt gebruikt om een lokaal volume aan een container te monteren. Lezen en schrijven zijn lokale operaties en snel. Gegevens worden gerepliceerd naar secundaire knooppunten, waardoor ze zeer beschikbaar zijn. Failover is ook snel. Wanneer een container vastloopt, mislukt deze naar een knooppunt dat al een kopie van uw gegevens heeft. Zie bijvoorbeeld [Hoe u een app implementeert met betrouwbaar volume van Service Fabric](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md).

### <a name="azure-files-volume"></a>Azure-bestandenvolume

Azure Files Volume is een Docker-volumestuurprogramma dat wordt gebruikt om een Azure Files-share aan een container te koppelen. Azure Files-opslag maakt gebruik van netwerkopslag, dus lezen en schrijven vinden plaats via het netwerk. In vergelijking met servicefabric betrouwbaar volume is Azure Files-opslag minder performant, maar biedt het een goedkopere en volledig betrouwbare gegevensoptie. Zie bijvoorbeeld [Hoe u een app implementeert met Azure Files Volume](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Volgende stappen

Zie Bronnen voor [servicefabric](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
