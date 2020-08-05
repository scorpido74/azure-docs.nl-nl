---
title: Inleiding tot Azure Spring Cloud
description: Ontdek de functies en voordelen van Azure Spring Cloud voor het implementeren en beheren van Java Spring-toepassingen in Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 3289233cb796d58972c2afd9a223075cedff265d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075989"
---
# <a name="what-is-azure-spring-cloud"></a>Wat is Azure Spring Cloud?

Met Azure Spring Cloud kunt u eenvoudig microservicetoepassingen op basis van Spring Boot implementeren in Azure zonder codewijzigingen.  Azure Spring Cloud beheert de levenscyclus van Spring Cloud-toepassingen, zodat ontwikkelaars zich kunnen richten op hun code.  Spring Cloud biedt levenscyclusbeheer met uitgebreide bewaking en diagnose, configuratiebeheer, servicedetectie, CI/CD-integratie, blue-green implementaties, en meer.

Als onderdeel van het Azure-ecosysteem kan Azure Spring Cloud eenvoudig worden verbonden met Azure-services, waaronder opslag, databases, bewaking en meer.

Azure Spring Cloud wordt momenteel aangeboden als openbare preview. Met openbare preview-aanbiedingen kunnen klanten voorafgaand aan de officiële release met nieuwe functies experimenteren.  Openbare preview-functies en -services zijn niet bedoeld voor gebruik in productie.  Voor meer informatie over ondersteuning tijdens previews kunt u onze [Veelgestelde vragen](https://azure.microsoft.com/support/faq/) raadplegen of een [Ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) indienen.

Om aan de slag te gaan, voert u de Spring Cloud-quickstart uit in de [Azure CLI](spring-cloud-quickstart-launch-app-cli.md), de [Azure-portal](spring-cloud-quickstart-launch-app-portal.md) of [Maven](spring-cloud-quickstart-launch-app-maven.md).

Meer voorbeelden zijn beschikbaar in GitHub: [Azure Spring Cloud-voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

## <a name="application-configuration"></a>Toepassingsconfiguratie

### <a name="spring-cloud-config-server"></a>Spring Cloud-configuratieserver

Azure Spring Cloud-configuratieserver biedt ondersteuning voor een geëxternaliseerde configuratie in een gedistribueerd systeem aan server- en clientzijde.  De configuratieserver biedt een centrale locatie voor het beheren van toepassingseigenschappen in alle omgevingen.  Ga voor meer informatie naar de [Naslaginformatie over Spring Cloud-configuratieserver](https://spring.io/projects/spring-cloud-config.md) en volg de zelfstudie.

### <a name="enable-bluegreen-deployments"></a>Blue-green implementaties inschakelen

Azure Spring Cloud ondersteunt blue-green implementaties voor het vrijgeven en bijwerken van code naar productieomgevingen.  Door gebruik te maken van dit patroon voor wijzigingsbeheer kunnen ontwikkelaars functie- en codewijzigingen implementeren met de zekerheid van een directe fallback wanneer dat nodig is.  Met Azure kunnen ontwikkelaars zich concentreren op het schrijven van code door meerdere productieomgevingen te beheren en codewijzigingen eenvoudig bij te werken of terug te draaien zonder de toepassing te onderbreken.  Zie dit [artikel met instructies](spring-cloud-howto-staging-environment.md) voor meer informatie over faseringsomgevingen en blue-green implementaties.

### <a name="automate-cicd-pipelines"></a>CI/CD-pijplijnen automatiseren

Azure Spring Cloud biedt integratie met Azure DevOps met behulp van de Azure CLI.  Met Azure DevOps kunt u code-integratie en -implementatie automatiseren in uw Spring-toepassing.  Lees [dit artikel](spring-cloud-howto-cicd.md) voor meer informatie.

### <a name="scale-your-application"></a>Uw toepassing schalen

Met Azure Spring Cloud kunt u eenvoudig de microservices in uw Azure Spring Cloud-dashboard schalen.  Het aantal Vcpu's en de hoeveelheid geheugen die beschikbaar is voor uw microservices kunnen omhoog of omlaag worden geschaald op basis van uw vereisten.  Schalen gaat binnen enkele seconden van kracht en vereist geen codewijzigingen of herimplementatie.  Volg deze [zelfstudie](spring-cloud-tutorial-scale-manual.md) voor meer informatie.

## <a name="application-monitoring"></a>Toepassingsbewaking

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Uw toepassing controleren met behulp van gedistribueerde tracering en Azure App Insights

Met de gedistribueerde traceringshulpprogramma's in Spring Cloud kunnen ontwikkelaars de complexe verbindingen tussen microservices in een toepassing bewaken en er fouten in opsporen.  Dankzij de integratie van [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) met [Application Insights](../azure-monitor/insights/insights-overview.md) van Azure biedt Azure krachtige functies voor gedistribueerde tracering rechtstreeks vanuit de Azure-portal.  Volg deze [zelfstudie](spring-cloud-tutorial-distributed-tracing.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Uw Spring Cloud-app starten vanuit de CLI](spring-cloud-quickstart-launch-app-cli.md)
