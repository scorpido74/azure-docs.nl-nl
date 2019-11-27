---
title: Azure Service Fabric-bewakings partners | Microsoft Docs
description: Meer informatie over het bewaken van Azure Service Fabric met oplossingen voor het controleren van partners
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: 8353c8846820aa996214e6b119a2f9027b8b6fe6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232453"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Azure Service Fabric-bewakings partners

In dit artikel wordt beschreven hoe u een Service Fabric toepassingen, clusters en infra structuur kunt bewaken met een aantal partner oplossingen. We hebben met elk van de onderstaande partners samen gewerkt voor het maken van geïntegreerde aanbiedingen voor Service Fabric.

## <a name="dynatrace"></a>Dynatrace

Onze integratie met Dynatrace biedt veel mogelijkheden van de box om uw Service Fabric-clusters te bewaken. Als u de Dynatrace-OneAgent op uw VMSS-instanties installeert, hebt u prestatie meter items en een topologie van uw Service Fabric-implementatie naar het app-niveau. Dynatrace is ook een uitstekende keuze voor on-premises bewaking. Bekijk meer van de functies die worden vermeld in de [aankondiging](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) en [instructies](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) om Dynatrace in uw cluster in te scha kelen. 

## <a name="datadog"></a>Datadog

Datadog heeft een uitbrei ding voor VMSS voor zowel Windows-als Linux-exemplaren. Met Datadog kunt u Windows-gebeurtenis logboeken verzamelen en Service Fabric platform gebeurtenissen in Windows verzamelen. Bekijk [hier](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric)de instructies voor het verzenden van uw diagnostische gegevens naar Datadog.

## <a name="appdynamics"></a>AppDynamics

De Service Fabric-integratie met AppDynamics is op toepassings niveau. Door omgevings variabelen bij te werken en app Dynamics NuGets te gebruiken, kunt u telemetrie van toepassingen naar AppDynamics verzenden. Raadpleeg deze [instructies](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) voor het integreren van uw .net service Fabric-toepassingen met AppDynamics.

## <a name="new-relic"></a>New Relic

Nieuwe Relic is een hulp programma voor het beheren van toepassings prestaties dat goed kan worden geïntegreerd met Service Fabric-toepassingen. U kunt de nieuwe Relic NuGet-pakketten installeren en specifieke omgevings variabelen toevoegen aan de manifest bestanden om de telemetrie van uw toepassing te verzenden naar een nieuwe Relic. Bekijk deze [instructies](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) om nieuwe Relic-telemetrie in te scha kelen voor uw .net service Fabric-toepassingen.

## <a name="elk"></a>ELK 

De ELK-stack is een verzameling van open-source technologieën: Elasticsearch, Logstash en Kibana. Door deze in combi natie te gebruiken, kunt u Service Fabric bewakings-en diagnostische gegevens verzamelen, opslaan en analyseren. [Hier vindt](service-fabric-tutorial-java-elk.md)u een zelf studie over hoe u dit doet met Service Fabric systeem eigen Java-toepassingen. 

## <a name="humio"></a>Humio

Humio is een logboek verzamelings service waarmee u logboeken van uw toepassingen en gebeurtenissen van Service Fabric in de Cloud of on-premises in realtime kunt verzamelen. Naast Live waarneem bare informatie biedt Humio de status van de functies voor het analyseren van illustraties en visualisaties voor het weer geven en verzamelen van inzichten van uw diagnostische gegevens. Humio heeft rendabele prijs plannen en is gebouwd om te worden geschaald terwijl de snelle snelheid wordt verlicht. Het wordt rechtstreeks geïntegreerd met Service Fabric platform gebeurtenissen en toepassings-telemetrie. U kunt [hier](https://github.com/humio/service-fabric-humio)meer lezen over de Humio-en service Fabric-integratie.

## <a name="next-steps"></a>Volgende stappen

* Bekijk een [overzicht van controle en diagnostische gegevens](service-fabric-diagnostics-overview.md) in service Fabric
* Meer informatie over het [oplossen van veelvoorkomende scenario's](service-fabric-diagnostics-common-scenarios.md) met onze eerste partij hulpprogramma's
