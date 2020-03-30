---
title: Aanbevolen procedures voor Azure Service Fabric-toepassingen en clustertoepassingen
description: Aanbevolen procedures en ontwerpoverwegingen voor het beheren van clusters, apps en services met Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551774"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Aanbevolen procedures voor Azure Service Fabric-toepassingen en clustertoepassingen

In dit artikel vindt u koppelingen naar aanbevolen procedures voor het beheer van Azure Service Fabric-toepassingen en -clusters. We raden u ten zeerste aan deze praktijken te implementeren om de betrouwbaarheid van uw productieomgeving te optimaliseren. Gebruik een van de [clustersjablonen servicestructuur](https://github.com/Azure-Samples/service-fabric-cluster-templates) om te beginnen met het ontwerpen van uw productieoplossing of om uw bestaande sjabloon bij te werken om deze praktijken op te nemen.

## <a name="security"></a>Beveiliging

* [Aanbevolen procedures voor beveiliging](service-fabric-best-practices-security.md)

## <a name="networking"></a>Netwerken

* [Aanbevolen procedures voor netwerken](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Compute-planning en schalen

* [Aanbevolen procedures voor compute scaling](service-fabric-best-practices-capacity-scaling.md)
* [Rekencapaciteitsplanning](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastructure als code

* [Aanbevolen procedures voor het implementeren van infrastructuur als code](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Controle en diagnose

* [Aanbevolen procedures voor clustermonitoring en -diagnostiek](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Toepassingsontwerp

* [Aanbevolen procedures voor toepassingsontwerp](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Controlelijst

Nadat u de in de vorige secties voorgestelde procedures hebt geïmplementeerd, moet u ervoor zorgen dat u alle aanbevolen procedures hebt geïntegreerd in de checklist voor productiegereedheid:
* [Checklist voor productiegereedheid azure service fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Volgende stappen

* Een cluster maken op VM's of computers met Windows Server: [clustercreatie van servicefabric voor Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Een cluster maken op VM's of computers met Linux: [een Linux-cluster maken](service-fabric-cluster-creation-via-portal.md)
* Problemen met de servicefabric: [handleidingen voor het oplossen van problemen oplossen](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)