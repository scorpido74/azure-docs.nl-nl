---
title: Inleiding tot Azure Spring Cloud
description: Ontdek de functies en voordelen van Azure Spring Cloud voor het implementeren en beheren van Java Spring-toepassingen in Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: aa3f1032301224701f5bfc08807e89194f263da6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255236"
---
# <a name="what-is-azure-spring-cloud"></a>Wat is Azure Spring Cloud?

Met Azure Spring Cloud kunt u eenvoudig microservicetoepassingen op basis van Spring Boot implementeren in Azure zonder codewijzigingen.  Azure Spring Cloud beheert de infrastructuur van Spring Cloud-toepassingen, zodat ontwikkelaars zich kunnen richten op hun code.  Spring Cloud biedt levenscyclusbeheer met uitgebreide bewaking en diagnose, configuratiebeheer, servicedetectie, CI/CD-integratie, blue-green implementaties, en meer.

Als onderdeel van het Azure-ecosysteem kan Azure Spring Cloud eenvoudig worden verbonden met Azure-services, waaronder opslag, databases, bewaking en meer.

In deze introductie wordt de Azure Spring Cloud-configuratieserver beschreven en leest u hoe u blauwe/groene implementaties inschakelt, de schaal van apps aanpast en de prestaties van apps bewaakt.

## <a name="spring-cloud-config-server"></a>Spring Cloud-configuratieserver

Azure Spring Cloud-configuratieserver biedt ondersteuning voor een geëxternaliseerde configuratie in een gedistribueerd systeem aan server- en clientzijde.  De Azure Spring Cloud-configuratieserver is een centrale locatie voor het beheren van toepassingseigenschappen in alle omgevingen. Raadpleeg de [Spring Cloud Config Server-referentie](https://spring.io/projects/spring-cloud-config.md) voor meer informatie. 

## <a name="enable-bluegreen-deployments"></a>Blue-green implementaties inschakelen

Azure Spring Cloud ondersteunt blue-green implementaties voor het vrijgeven en bijwerken van code naar productieomgevingen.  Door dit patroon voor wijzigingsbeheer kunnen ontwikkelaars functie- en codewijzigingen implementeren met de zekerheid van een directe fallback wanneer dat nodig is.  Ontwikkelaars kunnen zich concentreren op het schrijven van code met meerdere productieomgevingen om codewijzigingen bij te werken of terug te draaien zonder de toepassing te onderbreken.  Zie dit [artikel met instructies](spring-cloud-howto-staging-environment.md) voor meer informatie over faseringsomgevingen en blue-green implementaties.

## <a name="automate-cicd-pipelines"></a>CI/CD-pijplijnen automatiseren

Azure Spring Cloud biedt integratie met Azure DevOps met behulp van de Azure CLI.  Met Azure DevOps kunt u code-integratie en -implementatie automatiseren in uw Spring-toepassing.  Lees [dit artikel](spring-cloud-howto-cicd.md) voor meer informatie.

## <a name="scale-your-application"></a>Uw toepassing schalen

Met Azure Spring Cloud kunt u eenvoudig de microservices in uw Azure Spring Cloud-dashboard schalen.  Het aantal Vcpu's en de hoeveelheid geheugen die beschikbaar is voor uw microservices kunnen omhoog of omlaag worden geschaald op basis van uw vereisten.  Schalen gaat binnen enkele seconden van kracht en vereist geen codewijzigingen of herimplementatie.  Volg deze [zelfstudie](spring-cloud-tutorial-scale-manual.md) voor meer informatie.

## <a name="application-monitoring"></a>Toepassingsbewaking

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Uw toepassing controleren met behulp van gedistribueerde tracering en Azure App Insights

Met de gedistribueerde traceringshulpprogramma's in Spring Cloud kunnen ontwikkelaars de complexe verbindingen tussen microservices in een toepassing bewaken en er fouten in opsporen.  Dankzij de integratie van [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) met [Application Insights](../azure-monitor/insights/insights-overview.md) van Azure biedt Azure krachtige functies voor gedistribueerde tracering rechtstreeks vanuit de Azure-portal.  Volg deze [zelfstudie](spring-cloud-tutorial-distributed-tracing.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Voltooi de Spring Cloud-quickstart om aan de slag te gaan:
> [!div class="nextstepaction"]
> [Snelstart: Uw eerste Azure Spring Cloud-toepassing implementeren](spring-cloud-quickstart.md)

Meer voorbeelden zijn beschikbaar in GitHub: [Azure Spring Cloud-voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
