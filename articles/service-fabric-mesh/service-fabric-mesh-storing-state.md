---
title: Opties voor status opslag op Azure Service Fabric mesh
description: Meer informatie over de status van een betrouw bare opslag in Service Fabric mesh-toepassingen die worden uitgevoerd op Azure Service Fabric net.
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 0b0bd611fa86d155bb5bf2e07808e7365e28871c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78397265"
---
# <a name="state-management-with-service-fabric"></a>Status beheer met Service Fabric

Service Fabric ondersteunt veel verschillende opties voor status opslag. Zie [service Fabric concepten: State](/azure/service-fabric/service-fabric-concepts-state)(Engelstalig) voor een conceptueel overzicht van de patronen voor status beheer en service Fabric. Al deze concepten zijn van toepassing, ongeacht of uw services binnen of buiten Service Fabric net worden uitgevoerd. 

Met Service Fabric net kunt u eenvoudig een nieuwe toepassing implementeren en deze verbinden met een bestaande gegevens opslag die wordt gehost in Azure. Naast het gebruik van een externe data base, zijn er verschillende opties voor het opslaan van gegevens, afhankelijk van of de service lokale of externe opslag wenst. 

## <a name="volumes"></a>Volumes

Containers maken vaak gebruik van tijdelijke schijven. Tijdelijke schijven zijn echter tijdelijk een nieuwe tijdelijke schijf en verliezen de informatie wanneer een container vastloopt. Het is ook lastig om informatie over tijdelijke schijven met andere containers te delen. Volumes zijn mappen die in de container instanties worden gekoppeld die u kunt gebruiken om de status te behouden. Volumes bieden u bestands opslag voor algemeen gebruik en bieden de mogelijkheid om bestanden te lezen/schrijven met behulp van normale I/O-bestand-Api's van de schijf. De volume resource beschrijft hoe een directory moet worden gekoppeld en welke back-ups van opslag moet worden gebruikt. U kunt kiezen voor Azure file storage of Service Fabric volume schijf om gegevens op te slaan.

![Volumes][image3]

### <a name="service-fabric-reliable-volume"></a>Betrouw bare volume Service Fabric

Service Fabric betrouw bare volume is een docker-volume stuur programma dat wordt gebruikt om een lokaal volume te koppelen aan een container. Lees-en schrijf bewerkingen zijn lokale bewerkingen en snelle. Gegevens worden naar secundaire knoop punten gerepliceerd, waardoor deze Maxi maal beschikbaar is. Failover is ook snel. Wanneer een container vastloopt, wordt een failover uitgevoerd naar een knoop punt dat al een kopie van uw gegevens heeft. Zie [een app implementeren met Service Fabric betrouw bare volume](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md)voor een voor beeld.

### <a name="azure-files-volume"></a>Azure Files volume

Azure Files volume is een docker-volume stuur programma dat wordt gebruikt om een Azure Files share te koppelen aan een container. Azure Files opslag gebruikt netwerk opslag, zodat Lees-en schrijf bewerkingen via het netwerk plaatsvinden. Vergeleken met Service Fabric betrouw bare volume, is Azure Files opslag minder goed, maar biedt een optie voor ingeschikte en volledig betrouw bare gegevens. Zie [een app implementeren met Azure files volume](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)voor een voor beeld.

## <a name="next-steps"></a>Volgende stappen

Zie [service Fabric resources](service-fabric-mesh-service-fabric-resources.md) voor meer informatie over het toepassings model.

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
