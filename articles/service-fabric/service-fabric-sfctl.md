---
title: Azure Service Fabric CLI- sfctl
description: Meer informatie over sfctl, de opdrachtregelinterface van Azure Service Fabric. Bevat een lijst met opdrachten en subgroepen.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 56efa15a7de3414f9c535e66bd80c94594cd5038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906216"
---
# <a name="sfctl"></a>sfctl
Opdrachten voor het beheren van clusterclusters en entiteiten voor servicefabric. Deze versie is compatibel met Service Fabric 7.0 runtime.

Commando's volgen het nounwerkwoordpatroon. Zie subgroepen voor meer informatie.

## <a name="subgroups"></a>Subgroepen
|Deelgroep|Beschrijving|
| --- | --- |
| [toepassing](service-fabric-sfctl-application.md) | Toepassingen en toepassingstypen maken, verwijderen en beheren. |
| [Chaos](service-fabric-sfctl-chaos.md) | Start, stop en rapporteer over de chaostestservice. |
| [Cluster](service-fabric-sfctl-cluster.md) | Selecteer, beheer en beheer Service Fabric-clusters. |
| [Opstellen](service-fabric-sfctl-compose.md) | Docker Compose-toepassingen maken, verwijderen en beheren. |
| [container](service-fabric-sfctl-container.md) | Containergerelateerde opdrachten uitvoeren op een clusterknooppunt. |
| [Gebeurtenissen](service-fabric-sfctl-events.md) | Gebeurtenissen ophalen uit de evenementenwinkel (als de EventStore-service al is geïnstalleerd). |
| [Is](service-fabric-sfctl-is.md) | Opdrachten opvragen en verzenden naar de infrastructuurservice. |
| [mesh](service-fabric-sfctl-mesh.md) | Service Fabric Mesh-toepassingen verwijderen en beheren. |
| [Knooppunt](service-fabric-sfctl-node.md) | Beheer de knooppunten die een cluster vormen. |
| [Partitie](service-fabric-sfctl-partition.md) | Partities voor elke service opvragen en beheren. |
| [Eigenschap](service-fabric-sfctl-property.md) | Opslag- en queryeigenschappen onder namen van servicefabric. |
| [Replica](service-fabric-sfctl-replica.md) | Beheer de replica's die behoren tot servicepartities. |
| [Rpm](service-fabric-sfctl-rpm.md) | Beveel opdrachten op en stuur ze naar de service voor reparatiebeheer. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Beheer zelfstandige Service Fabric-clusters. |
| [Service](service-fabric-sfctl-service.md) | Service-, servicetypen en servicepakketten maken, verwijderen en beheren. |
| [instellingen](service-fabric-sfctl-settings.md) | Instellingen lokaal configureren voor dit exemplaar van sfctl. |
| [store](service-fabric-sfctl-store.md) | Voer bewerkingen op basisbestandsniveau uit in het clusterimagearchief. |

## <a name="next-steps"></a>Volgende stappen
- Stel de SERVICE Fabric CLI [in.](service-fabric-cli.md)
- Meer informatie over het gebruik van de CLI van de ServiceFabric met behulp van de [voorbeeldscripts](/azure/service-fabric/scripts/sfctl-upgrade-application).