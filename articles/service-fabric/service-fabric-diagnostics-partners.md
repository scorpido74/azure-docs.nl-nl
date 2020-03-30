---
title: Azure Service Fabric-bewakingspartners
description: Meer informatie over het bewaken van Azure Service Fabric-toepassingen, -clusters en -infrastructuur met oplossingen voor partnerbewaking.
author: srrengar
ms.topic: article
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: fd24d65ebdf4e458870819286024d1ea2e13d83e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645715"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Azure Service Fabric-bewakingspartners

Dit artikel laat zien hoe men hun Service Fabric-toepassingen, clusters en infrastructuur kan monitoren met een handvol partneroplossingen. We hebben met elk van de onderstaande partners samengewerkt om geïntegreerde aanbiedingen voor Service Fabric te creëren.

## <a name="dynatrace"></a>Dynatrace

Onze integratie met Dynatrace biedt veel kant-en-klare functies om uw Service Fabric-clusters te bewaken. Als u de Dynatrace OneAgent op uw VMSS-exemplaren installeert, u prestatiemeteritems en een topologie van uw Service Fabric-implementatie tot op app-niveau uitvoeren. Dynatrace is ook een goede keuze voor on-premises monitoring. Bekijk meer van de functies in de [aankondiging](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) en [instructies](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) om Dynatrace in te schakelen op uw cluster. 

## <a name="datadog"></a>Datadog (Datadog)

Datadog heeft een extensie voor VMSS voor zowel Windows- als Linux-exemplaren. Met Datadog u Windows-gebeurtenislogboeken verzamelen en zo Service Fabric-platformgebeurtenissen op Windows verzamelen. Bekijk [hier](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric)de instructies voor het verzenden van uw diagnostische gegevens naar Datadog.

## <a name="appdynamics"></a>AppDynamics

De Service Fabric-integratie met AppDynamics bevindt zich op toepassingsniveau. Door omgevingsvariabelen bij te werken en App Dynamics NuGets te gebruiken, u toepassingstelemetrie naar AppDynamics verzenden. Raadpleeg deze [instructies](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) voor het integreren van uw .NET Service Fabric-toepassingen met AppDynamics.

## <a name="new-relic"></a>New Relic

New Relic is een andere Application Performance Management tool die goed integreert met Service Fabric applicaties. U de New Relic NuGet-pakketten installeren en specifieke omgevingsvariabelen toevoegen in uw manifestbestanden om uw applicatie telemetrie naar New Relic te sturen. Bekijk deze [instructies](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) om Nieuwe Relic-telemetrie in te schakelen voor uw .NET Service Fabric-toepassingen.

## <a name="elk"></a>ELANDEN 

De ELK stack is een verzameling van open-source technologieën: Elasticsearch, Logstash en Kibana. Door deze technologieën in combinatie te gebruiken, u servicefabric-monitoring- en diagnostische gegevens verzamelen, opslaan en analyseren. We hebben een tutorial voor hoe dit te doen met Service Fabric native Java-toepassingen [hier](service-fabric-tutorial-java-elk.md). 

## <a name="humio"></a>Humio (Humio)

Humio is een logcollectieservice die logboeken van uw toepassingen en gebeurtenissen van Service Fabric in de cloud of on-premises in realtime kan verzamelen. Naast de live observability biedt Humio state-of-the-art analyse- en visualisatiemogelijkheden voor het bekijken en verzamelen van informatie uit uw diagnostiek. Humio heeft kosteneffectieve prijsplannen en is gebouwd op schaal met behoud van de snelle snelheid. Het integreert direct met Service Fabric platform evenementen en Application telemetrie. U [hier](https://github.com/humio/service-fabric-humio)meer lezen over de Integratie van Humio en Service Fabric.

## <a name="next-steps"></a>Volgende stappen

* Krijg een [overzicht van monitoring en diagnostiek](service-fabric-diagnostics-overview.md) in Service Fabric
* Meer informatie over het [diagnosticeren van veelvoorkomende scenario's](service-fabric-diagnostics-common-scenarios.md) met onze first party-tools
