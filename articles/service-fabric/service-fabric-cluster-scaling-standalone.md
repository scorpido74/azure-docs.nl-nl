---
title: Zelfstandige clusterschaling van Azure Service Fabric
description: Meer informatie over het schalen van zelfstandige clusters van Service Fabric in of uit en omhoog of omlaag.
author: dkkapur
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dekapur
ms.openlocfilehash: 16ec0eb429ec6e8f6613490226b7cff01dff1b32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451917"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Zelfstandige clusters van servicefabric schalen
Een Service Fabric-cluster is een met het netwerk verbonden set virtuele of fysieke machines waarin uw microservices worden geïmplementeerd en beheerd. Een machine of VM die deel uitmaakt van een cluster wordt een knooppunt genoemd. Clusters kunnen mogelijk duizenden knooppunten bevatten. Nadat u een cluster servicestructuur hebt gemaakt, u het cluster horizontaal schalen (het aantal knooppunten wijzigen) of verticaal wijzigen (de bronnen van de knooppunten wijzigen).  U het cluster op elk gewenst moment schalen, zelfs wanneer workloads op het cluster worden uitgevoerd.  Naarmate het cluster schaalt, schalen uw toepassingen ook automatisch.

Waarom het cluster schalen? Toepassingseisen veranderen in de loop van de tijd.  Mogelijk moet u de clusterresources vergroten om te voldoen aan de toegenomen toepassingsworkload of het netwerkverkeer of clusterbronnen verlagen wanneer de vraag daalt.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>In- en uitschalen of horizontaal schalen
Hiermee wijzigt u het aantal knooppunten in het cluster.  Zodra de nieuwe knooppunten lid worden van het cluster, verplaatst [het Clusterresourcebeheer](service-fabric-cluster-resource-manager-introduction.md) services naar deze knooppunten, waardoor de belasting op de bestaande knooppunten wordt verminderd.  U ook het aantal knooppunten verminderen als de bronnen van het cluster niet efficiënt worden gebruikt.  Als knooppunten het cluster verlaten, gaan services van die knooppunten af en neemt de belasting op de resterende knooppunten toe.  Het verminderen van het aantal knooppunten in een cluster dat in Azure wordt uitgevoerd, kan u geld besparen, omdat u betaalt voor het aantal VM's dat u gebruikt en niet de werkbelasting op die VM's.  

- Voordelen: Oneindige schaal, in theorie.  Als uw toepassing is ontworpen voor schaalbaarheid, u onbeperkte groei mogelijk maken door meer knooppunten toe te voegen.  De tooling in cloudomgevingen maakt het eenvoudig om knooppunten toe te voegen of te verwijderen, zodat u eenvoudig de capaciteit aanpassen en u alleen betaalt voor de resources die u gebruikt.  
- Nadelen: Toepassingen moeten worden [ontworpen voor schaalbaarheid.](service-fabric-concepts-scalability.md)  Toepassingsdatabases en persistentie kunnen ook extra architectonische werkzaamheden vereisen om te schalen.  [Betrouwbare collecties](service-fabric-reliable-services-reliable-collections.md) in servicefabric-stateful services maken het echter veel gemakkelijker om uw toepassingsgegevens te schalen.

Met zelfstandige clusters u servicestructuurcluster on-premises of in de cloudprovider van uw keuze implementeren.  Node-typen bestaan uit fysieke machines of virtuele machines, afhankelijk van uw implementatie. In vergelijking met clusters die in Azure worden uitgevoerd, is het proces van het schalen van een standalone cluster iets meer betrokken.  U moet handmatig het aantal knooppunten in het cluster wijzigen en vervolgens een upgrade van de clusterconfiguratie uitvoeren.

Verwijdering van knooppunten kan meerdere upgrades initiëren. Sommige knooppunten zijn `IsSeedNode=”true”` gemarkeerd met tag en kunnen worden geïdentificeerd door het clustermanifest op te vragen met [Get-ServiceClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). Het verwijderen van dergelijke knooppunten kan langer duren dan andere, omdat de zaadknooppunten in dergelijke scenario's moeten worden verplaatst. Het cluster moet minimaal drie primaire knooppuntknooppunten behouden.

> [!WARNING]
> We raden u aan het aantal knooppunten niet onder de [clustergrootte van de betrouwbaarheidslaag](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) voor het cluster te verlagen. Dit zal interfereren met het vermogen van de Service Fabric System Services te worden gerepliceerd over het cluster, en zal destabiliseren of eventueel vernietigen van het cluster.
>

Houd bij het schalen van een zelfstandig cluster rekening met de volgende richtlijnen:
- De vervanging van primaire knooppunten moet worden uitgevoerd een knooppunt na het andere, in plaats van het verwijderen en vervolgens toe te voegen in batches.
- Controleer voordat u een knooppunttype verwijdert, of er knooppunten zijn die verwijzen naar het knooppunttype. Verwijder deze knooppunten voordat u het bijbehorende knooppunttype verwijdert. Zodra alle bijbehorende knooppunten zijn verwijderd, u het NodeType uit de clusterconfiguratie verwijderen en een configuratie-upgrade starten met [Start-ServiceFabricClusterConfigurationUpgrade.](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade)

Zie [Een zelfstandig cluster schalen](service-fabric-cluster-windows-server-add-remove-nodes.md)voor meer informatie.

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Op- en omlaag schalen of verticaal schalen 
Hiermee wijzigt u de bronnen (CPU, geheugen of opslag) van knooppunten in het cluster.
- Voordelen: Software- en applicatiearchitectuur blijft hetzelfde.
- Nadelen: Eindige schaal, omdat er een limiet is aan hoeveel u resources op afzonderlijke knooppunten verhogen. Downtime, omdat u fysieke of virtuele machines offline moet halen om resources toe te voegen of te verwijderen.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [schaalbaarheid van toepassingen](service-fabric-concepts-scalability.md).
* [Een Azure-cluster in- of uitchecken](service-fabric-tutorial-scale-cluster.md).
* [Schaal een Azure-cluster programmatisch](service-fabric-cluster-programmatic-scaling.md) met de vloeiende Azure compute SDK.
* [Een zelfstandig cluster in- of uitchecken](service-fabric-cluster-windows-server-add-remove-nodes.md).

