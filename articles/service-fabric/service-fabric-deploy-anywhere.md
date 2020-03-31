---
title: Overzicht van Azure- en zelfstandige Service Fabric-clusters
description: "U Service Fabric-clusters maken op alle VM's of computers met Windows Server of Linux. Dit betekent dat u Service Fabric-toepassingen implementeren en uitvoeren in elke omgeving waar u een set Windows Server- of Linux-computers hebt die met elkaar verbonden zijn: on-premises, Microsoft Azure of met elke cloudprovider."
author: dkkapur
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: a3627effe10039ded5007f9dd060bf1865929040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751154"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Azure- en standalone Service Fabric-clusters vergelijken op Windows Server en Linux

Een Service Fabric-cluster is een met het netwerk verbonden set virtuele of fysieke machines waarin uw microservices worden geïmplementeerd en beheerd. Een machine of VM die deel uitmaakt van een cluster, wordt een clusterknooppunt genoemd. Clusters kunnen worden geschaald naar duizenden knooppunten. Als u nieuwe knooppunten aan het cluster toevoegt, brengt Service Fabric de replica's en instanties van de servicepartitie opnieuw in evenwicht in het toegenomen aantal knooppunten. De algehele prestaties van toepassingen verbeteren en de discussie over de toegang tot het geheugen neemt af. Als de knooppunten in het cluster niet efficiënt worden gebruikt, u het aantal knooppunten in het cluster verminderen. Service Fabric brengt de partitiereplica's en instanties opnieuw in evenwicht in het verminderde aantal knooppunten om beter gebruik te maken van de hardware op elk knooppunt.

Service Fabric maakt het mogelijk om Service Fabric-clusters te maken op alle VM's of computers met Windows Server of Linux. Dit betekent dat u Service Fabric-toepassingen implementeren en uitvoeren in elke omgeving waar u een set Windows Server- of Linux-computers hebt die met elkaar verbonden zijn, of het nu on-premises, Microsoft Azure of met een cloudprovider is.

## <a name="benefits-of-clusters-on-azure"></a>Voordelen van clusters op Azure

Op Azure bieden we integratie met andere Azure-functies en -services, waardoor bewerkingen en beheer van het cluster eenvoudiger en betrouwbaarder worden.

* **Azure-portal:** Azure-portal maakt het eenvoudig om clusters te maken en te beheren.
* **Azure Resource Manager:** Met het gebruik van Azure Resource Manager u eenvoudig alle resources beheren die door het cluster als eenheid worden gebruikt en worden kostenbijhouden en facturering vereenvoudigd.
* **Cluster van servicestructuur als Azure-bron** Een cluster van Servicefabric is een Azure-bron, zodat u deze modelleren zoals u andere bronnen in Azure doet.
* **Integratie met Azure-infrastructuur** Service Fabric coördineert met de onderliggende Azure-infrastructuur voor os, netwerk en andere upgrades om de beschikbaarheid en betrouwbaarheid van uw toepassingen te verbeteren.  
* **Diagnostiek:** Op Azure bieden we integratie met Azure-diagnostische gegevens en Azure Monitor-logboeken.
* **Automatisch schalen:** Voor clusters op Azure bieden we ingebouwde functionaliteit voor automatisch schalen dankzij virtuele machine-schaalsets. In on-premises en andere cloudomgevingen moet u uw eigen functie voor automatisch schalen bouwen of handmatig schalen met behulp van de API's die Service Fabric blootlegt voor het schalen van clusters.

## <a name="benefits-of-standalone-clusters"></a>Voordelen van zelfstandige clusters

* U gratis een cloudprovider kiezen om uw cluster te hosten.
* Service Fabric-toepassingen, eenmaal geschreven, kunnen worden uitgevoerd in meerdere hostingomgevingen met minimale tot geen wijzigingen.
* Kennis van het bouwen van Service Fabric-toepassingen wordt van de ene hostingomgeving naar de andere overgedragen.
* Operationele ervaring met het beheren en beheren van Service Fabric clusters neemt over van de ene omgeving naar de andere.
* Breed klantbereik wordt niet begrensd door beperkingen in de omgeving.
* Er bestaat een extra laag van betrouwbaarheid en bescherming tegen wijdverspreide storingen, omdat u de services verplaatsen naar een andere implementatieomgeving als een datacenter of cloudprovider een black-out heeft.

## <a name="next-steps"></a>Volgende stappen

* Lees het overzicht van clusters van [ServiceFabric op Azure](service-fabric-azure-clusters-overview.md)
* Lees het overzicht van zelfstandige clusters van [Service Fabric](service-fabric-standalone-clusters-overview.md)
* Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md)