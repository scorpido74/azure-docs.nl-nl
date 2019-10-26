---
title: Azure Service Fabric CLI-sfctl | Microsoft Docs
description: Hierin worden de Service Fabric CLI-sfctl-opdrachten beschreven.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 7e7fc7bbc65e92960d7839f6531ef1f7c1935ed3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900883"
---
# <a name="sfctl"></a>sfctl
Opdrachten voor het beheren van Service Fabric clusters en entiteiten. Deze versie is compatibel met Service Fabric 6,5-runtime.

Opdrachten volgen het patroon van de zelfstandig naam woord. Zie subgroepen voor meer informatie.

## <a name="subgroups"></a>Subgroepen
|Subgroep|Beschrijving|
| --- | --- |
| [modules](service-fabric-sfctl-application.md) | Toepassingen en toepassings typen maken, verwijderen en beheren. |
| [chaos](service-fabric-sfctl-chaos.md) | Starten, stoppen en rapporteren op de chaos-test service. |
| [cluster](service-fabric-sfctl-cluster.md) | Service Fabric clusters selecteren, beheren en uitvoeren. |
| [Compose](service-fabric-sfctl-compose.md) | Docker opstellen toepassingen maken, verwijderen en beheren. |
| [verpakking](service-fabric-sfctl-container.md) | Container gerelateerde opdrachten uitvoeren op een cluster knooppunt. |
| [evenementen](service-fabric-sfctl-events.md) | Gebeurtenissen ophalen uit het gebeurtenissen archief (als de Event Store-service al is geïnstalleerd). |
| [ontbreekt](service-fabric-sfctl-is.md) | Query's uitvoeren en opdrachten verzenden naar de infrastructuur service. |
| [mesh](service-fabric-sfctl-mesh.md) | Service Fabric-mesh-toepassingen verwijderen en beheren. |
| [subknooppuntsleutels](service-fabric-sfctl-node.md) | De knoop punten beheren die een cluster vormen. |
| [partitie](service-fabric-sfctl-partition.md) | Zoek en beheer partities voor elke service. |
| [eigenschap](service-fabric-sfctl-property.md) | Eigenschappen voor opslaan en opvragen onder Service Fabric namen. |
| [exacte](service-fabric-sfctl-replica.md) | De replica's beheren die deel uitmaken van service partities. |
| [rpm](service-fabric-sfctl-rpm.md) | Query's uitvoeren en opdrachten verzenden naar de service reparatie beheer. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Zelfstandige Service Fabric-clusters beheren. |
| [service](service-fabric-sfctl-service.md) | Services, service typen en service pakketten maken, verwijderen en beheren. |
| [Instellingen](service-fabric-sfctl-settings.md) | Instellingen lokaal configureren voor dit exemplaar van sfctl. |
| [Store](service-fabric-sfctl-store.md) | Voer elementaire bewerkingen op bestands niveau uit in het cluster installatie kopie archief. |

## <a name="next-steps"></a>Volgende stappen
- [Stel](service-fabric-cli.md) de service Fabric cli in.
- Meer informatie over het gebruik van de Service Fabric CLI met behulp van de [voorbeeld scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).