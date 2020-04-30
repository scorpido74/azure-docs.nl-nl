---
title: Inleiding tot Azure lente-Cloud
description: Meer informatie over de functies en voor delen van Azure lente-Cloud voor het implementeren en beheren van Java lente-toepassingen in Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.openlocfilehash: 4426044b3608be0ded378f4f56cbec6bc1948d75
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78273262"
---
# <a name="what-is-azure-spring-cloud"></a>Wat is Azure Spring Cloud?

Met Azure lente-Cloud kunt u eenvoudig micro service-toepassingen op basis van een Spring boot implementeren naar Azure met wijzigingen in de code.  Azure lente-Cloud beheert de levens cyclus van Lente-Cloud toepassingen, zodat ontwikkel aars zich kunnen richten op hun code.  Lente-Cloud biedt levenscyclus beheer met uitgebreide bewaking en diagnose, configuratie beheer, service detectie, CI/CD-integratie, Blue-groen implementaties en meer.

Als onderdeel van het Azure-ecosysteem kan Azure lente-Cloud eenvoudig worden gebonden aan andere Azure-Services, waaronder opslag, data bases, bewaking en meer.

Azure lente Cloud wordt momenteel aangeboden als een open bare preview. Met open bare preview-aanbiedingen kunnen klanten experimenteren met nieuwe functies vóór hun officiële release.  Open bare preview-functies en-services zijn niet bedoeld voor gebruik in productie omgevingen.  Raadpleeg voor meer informatie over ondersteuning tijdens previews onze [Veelgestelde vragen](https://azure.microsoft.com/support/faq/) of bestand a [ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) voor meer informatie.

Om aan de slag te gaan, voltooit u de lente-Cloud Snelstartgids met behulp van de [Azure cli](spring-cloud-quickstart-launch-app-cli.md), het [Azure Portal](spring-cloud-quickstart-launch-app-portal.md)of [maven](spring-cloud-quickstart-launch-app-maven.md).

Meer voor beelden zijn beschikbaar op GitHub: [Azure lente-Cloud voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).

## <a name="application-configuration"></a>Toepassings configuratie

### <a name="spring-cloud-config-server"></a>Lente Cloud configuratie server

Azure lente Cloud config server biedt externe configuratie in een gedistribueerd systeem met server-en client-side-ondersteuning.  De configuratie server biedt een centrale locatie voor het beheren van toepassings eigenschappen in alle omgevingen.  Ga voor meer informatie naar de [bron van de lente-Cloud configuratie server](https://spring.io/projects/spring-cloud-config.md) en voltooi de zelf studie.

### <a name="enable-bluegreen-deployments"></a>Blue/groen-implementaties inschakelen

Azure lente-Cloud ondersteunt Blue/groen-implementaties voor het vrijgeven en bijwerken van code naar productie omgevingen.  Door gebruik te maken van dit patroon voor veranderingen beheer kunnen ontwikkel aars functies en code wijzigingen implementeren met de beveiliging van een directe terugval wanneer dat nodig is.  Met Azure kunnen ontwikkel aars zich concentreren op het schrijven van code door het beheer van meerdere productie omgevingen en het eenvoudig bijwerken of terugdraaien van code wijzigingen zonder de toepassing te onderbreken.  Ga naar dit [artikel voor instructies](spring-cloud-howto-staging-environment.md)voor meer informatie over faserings omgevingen en Blue/groen-implementaties.

### <a name="automate-cicd-pipelines"></a>CI/CD-pijp lijnen automatiseren

Azure lente Cloud biedt integratie met Azure DevOps met behulp van de Azure CLI.  Met Azure DevOps kunt u code integratie en implementatie automatiseren in uw lente-toepassing.  Ga naar dit [artikel](spring-cloud-howto-cicd.md)voor meer informatie.

### <a name="scale-your-application"></a>Uw toepassing schalen

Met Azure lente Cloud kunt u eenvoudig de micro Services in uw Azure veer Cloud-dash board schalen.  Het aantal Vcpu's en de hoeveelheid geheugen die beschikbaar is voor uw micro Services kunnen omhoog of omlaag worden geschaald op basis van uw vereisten.  Schalen neemt in een paar seconden toe en vereist geen code wijzigingen of herimplementatie.  Voltooi deze [zelf studie](spring-cloud-tutorial-scale-manual.md)voor meer informatie.

## <a name="application-monitoring"></a>Toepassings bewaking

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Uw toepassing bewaken met gedistribueerde tracering en Azure-app inzichten

Met de gedistribueerde traceer hulpprogramma's van de lente-Cloud kunnen ontwikkel aars de complexe verbindingen tussen micro Services in een toepassing opsporen en bewaken.  Door de [Sleuth van veer Clouds](https://spring.io/projects/spring-cloud-sleuth) te integreren met de [Application Insights](../azure-monitor/insights/insights-overview.md)van Azure, biedt Azure krachtige functies voor gedistribueerde tracering rechtstreeks vanuit de Azure Portal.  Voltooi deze [zelf studie](spring-cloud-tutorial-distributed-tracing.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Start uw lente Cloud-app vanuit de CLI](spring-cloud-quickstart-launch-app-cli.md)
