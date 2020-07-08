---
title: Aanbevolen procedures voor Azure Service Fabric-toepassing en-cluster
description: Aanbevolen procedures en ontwerp overwegingen voor het beheren van clusters, apps en services met Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75551774"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Aanbevolen procedures voor Azure Service Fabric-toepassing en-cluster

Dit artikel bevat koppelingen naar aanbevolen procedures voor het beheren van Azure Service Fabric-toepassingen en-clusters. We raden u ten zeerste aan deze procedures te implementeren om de betrouw baarheid van uw productie omgeving te optimaliseren. Gebruik een van de [service Fabric cluster sjablonen](https://github.com/Azure-Samples/service-fabric-cluster-templates) om te beginnen met het ontwerpen van uw productie oplossing of werk uw bestaande sjabloon bij om deze procedures uit te voeren.

## <a name="security"></a>Beveiliging

* [Aanbevolen procedures voor beveiliging](service-fabric-best-practices-security.md)

## <a name="networking"></a>Netwerken

* [Aanbevolen procedures voor netwerken](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Compute-planning en schalen

* [Aanbevolen procedures voor reken schalen](service-fabric-best-practices-capacity-scaling.md)
* [Planning van de berekening van de capaciteit](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastructure als code

* [Aanbevolen procedures voor het implementeren van de infra structuur als code](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Controle en diagnose

* [Aanbevolen procedures voor het controleren van het cluster en diagnostische gegevens](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Toepassingsontwerp

* [Aanbevolen procedures voor het ontwerpen van toepassingen](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Controlelijst

Nadat u de procedures in de vorige secties hebt geïmplementeerd, moet u ervoor zorgen dat u alle aanbevolen procedures in de controle lijst voor productie voorbereiding hebt geïntegreerd:
* [Controle lijst voor productie voorbereiding van Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Volgende stappen

* Een cluster maken op Vm's of computers met Windows Server: [service Fabric cluster maken voor Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Een cluster maken op Vm's of computers met Linux: [een Linux-cluster maken](service-fabric-cluster-creation-via-portal.md)
* Problemen oplossen Service Fabric: [richt lijnen voor probleem oplossing](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)