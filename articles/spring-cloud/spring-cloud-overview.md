---
title: Inleiding tot Azure Spring Cloud
description: Ontdek de functies en voordelen van Azure Spring Cloud voor het implementeren en beheren van Java Spring-toepassingen in Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.openlocfilehash: 4426044b3608be0ded378f4f56cbec6bc1948d75
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78273262"
---
# <a name="what-is-azure-spring-cloud"></a>Wat is Azure Spring Cloud?

Azure Spring Cloud maakt het eenvoudig om op Spring Boot gebaseerde microservicetoepassingen te implementeren in Azure met nul codewijzigingen.  Azure Spring Cloud beheert de levenscyclus van Spring Cloud-toepassingen, zodat ontwikkelaars zich kunnen concentreren op hun code.  Spring Cloud biedt levenscyclusbeheer met uitgebreide monitoring en diagnose, configuratiebeheer, servicedetectie, CI/CD-integratie, blauwgroene implementaties en meer.

Als onderdeel van het Azure-ecosysteem maakt Azure Spring Cloud eenvoudige binding met andere Azure-services mogelijk, waaronder opslag, databases, bewaking en meer.

Azure Spring Cloud wordt momenteel aangeboden als een openbare preview. Openbare preview-aanbiedingen stellen klanten in staat om te experimenteren met nieuwe functies voordat ze officieel worden uitgebracht.  Openbare preview-functies en services zijn niet bedoeld voor productiegebruik.  Bekijk onze veelgestelde vragen over onze [veelgestelde vragen](https://azure.microsoft.com/support/faq/) voor meer informatie over ondersteuning tijdens previews of dien een [ondersteuningsverzoek](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) in voor meer informatie.

Om aan de slag te gaan, voltooit u de Spring Cloud snel met behulp van de [Azure CLI](spring-cloud-quickstart-launch-app-cli.md), de [Azure-portal](spring-cloud-quickstart-launch-app-portal.md)of [Maven](spring-cloud-quickstart-launch-app-maven.md).

Meer voorbeelden zijn beschikbaar op GitHub: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

## <a name="application-configuration"></a>Toepassingsconfiguratie

### <a name="spring-cloud-config-server"></a>Spring Cloud Config-server

Azure Spring Cloud Config Server biedt geexternaliseerde configuratie in een gedistribueerd systeem met zowel server- als clientondersteuning.  Config Server biedt een centrale locatie voor het beheren van toepassingseigenschappen in alle omgevingen.  Ga voor meer informatie naar de [bronwolkConfig Server-referentie](https://spring.io/projects/spring-cloud-config.md) en voltooi de zelfstudie.

### <a name="enable-bluegreen-deployments"></a>Blauw/groen-implementaties inschakelen

Azure Spring Cloud ondersteunt blauw/groene implementaties voor het vrijgeven en bijwerken van code naar productieomgevingen.  Door gebruik te maken van dit wijzigingsbeheerpatroon kunnen ontwikkelaars functies en codewijzigingen implementeren met de beveiliging van een onmiddellijke terugval wanneer dat nodig is.  Azure stelt ontwikkelaars in staat zich te concentreren op het schrijven van code door meerdere productieomgevingen te beheren en het eenvoudig te maken om wijzigingen in code bij te werken of terug te draaien zonder de toepassing te onderbreken.  Ga voor meer informatie over omgevingen voor fasering en blauw/groene implementaties naar dit [artikel How-To.](spring-cloud-howto-staging-environment.md)

### <a name="automate-cicd-pipelines"></a>CI/CD-pijplijnen automatiseren

Azure Spring Cloud biedt integratie met Azure DevOps met behulp van de Azure CLI.  Met Azure DevOps u code-integratie en implementatie naar uw Spring-toepassing automatiseren.  Ga voor meer informatie naar dit [artikel.](spring-cloud-howto-cicd.md)

### <a name="scale-your-application"></a>Uw toepassing schalen

Met Azure Spring Cloud u eenvoudig de microservices in uw Azure Spring Cloud-dashboard schalen.  Zowel het aantal vCPU's als de hoeveelheid geheugen die beschikbaar is voor uw microservices kunnen worden opgeschaald of omlaag om aan uw behoeften te voldoen.  Schalen wordt binnen enkele seconden van kracht en vereist geen codewijzigingen of herplaatsing.  Vul deze [zelfstudie](spring-cloud-tutorial-scale-manual.md)voor meer informatie in.

## <a name="application-monitoring"></a>Toepassingsbewaking

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Uw toepassing bewaken met gedistribueerde tracering en Azure App Insights

Met de gedistribueerde traceringstools van Spring Cloud kunnen ontwikkelaars de complexe interconnecties tussen microservices in een toepassing debuggen en bewaken.  Door [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) te integreren met Azure's [Application Insights](../azure-monitor/insights/insights-overview.md)biedt Azure krachtige gedistribueerde traceringsmogelijkheden rechtstreeks vanuit de Azure-portal.  Vul deze [zelfstudie](spring-cloud-tutorial-distributed-tracing.md)voor meer informatie in.

## <a name="next-steps"></a>Volgende stappen

- [Start je Spring Cloud app vanuit de CLI](spring-cloud-quickstart-launch-app-cli.md)
