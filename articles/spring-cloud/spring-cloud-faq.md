---
title: Veelgestelde vragen over Azure Spring Cloud | Microsoft Documenten
description: In dit artikel worden veelgestelde vragen over Azure Spring Cloud beantwoord.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 62623bcadb35f21117ddc2601195e34598c2dff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298759"
---
# <a name="azure-spring-cloud-faq"></a>Veelgestelde vragen over Azure Spring Cloud

In dit artikel worden veelgestelde vragen over Azure Spring Cloud beantwoord. 

## <a name="general"></a>Algemeen

### <a name="why-azure-spring-cloud"></a>Waarom Azure Spring Cloud?

Azure Spring Cloud biedt een platform as a service (PaaS) voor Spring Cloud-ontwikkelaars. Azure Spring Cloud beheert uw toepassingsinfrastructuur, zodat u zich concentreren op toepassingscode en bedrijfslogica. Kernfuncties die zijn ingebouwd in Azure Spring Cloud zijn Eureka, Config Server, Service Registry Server, Pivotal Build Service, Blauwgroene implementaties en meer. Met deze service kunnen ontwikkelaars hun toepassingen ook binden met andere Azure-services, zoals Azure Cosmos DB, Azure Database voor MySQL en Azure Cache voor Redis.

Azure Spring Cloud verbetert de ervaring met applicatiediagnostiek voor ontwikkelaars en operators door Azure Monitor, Application Insights en Log Analytics te integreren.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Welke serviceplannen biedt Azure Spring Cloud?

Azure Spring Cloud biedt één serviceplan tijdens de previewperiode.  Een Spring Cloud-implementatie bevat 16 vCPU-cores en 32 gigabyte (GB) geheugen.  De bovengrens voor elke microservice-instantie binnen een implementatie is 4 vCPU-cores met 8 GB geheugen.

Resource | Aantal
------- | -------
App-exemplaren per voorjaarstoepassing | 20
Totaal aantal app-exemplaren per Azure Spring Cloud-serviceinstantie | 500
Azure Spring Cloud-service-exemplaren per regio per abonnement | 10
Permanente volumes | 10 x 50 GBytes

\*_Open een [ondersteuningsticket](https://azure.microsoft.com/support/faq/)om de limiet te verhogen._

Zie [Veelgestelde vragen over Azure Support](https://azure.microsoft.com/support/faq/)voor meer informatie.

### <a name="how-secure-is-azure-spring-cloud"></a>Hoe veilig is Azure Spring Cloud?

Beveiliging en privacy zijn een van de topprioriteiten voor Azure- en Azure Spring Cloud-klanten. Azure zorgt ervoor dat alleen klanten toegang hebben tot toepassingsgegevens, logboeken of configuraties door al deze gegevens veilig te versleutelen. Alle service-exemplaren in Azure Spring Cloud zijn van elkaar geïsoleerd.

Azure Spring Cloud biedt volledig TLS/SSL- en certificaatbeheer.

Kritieke beveiligingspatches voor OpenJDK- en Spring Cloud-runtimes worden zo snel mogelijk toegepast op Azure Spring Cloud.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>In welke regio's is Azure Spring Cloud beschikbaar?

Oost-VS, West-VS 2, West-Europa en Zuidoost-Azië.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Wat zijn de bekende beperkingen van Azure Spring Cloud?

Tijdens preview-release heeft Azure Spring Cloud de volgende bekende beperkingen:

* `spring.application.name`wordt overschreven door de toepassingsnaam die wordt gebruikt om elke toepassing te maken.
* `server.port`is niet toegestaan in het configuratiebestand van de Git repo. Als u het toevoegt aan het configuratiebestand, wordt uw toepassing waarschijnlijk onbereikbaar vanaf andere toepassingen of internet.
* De Azure-portal- en Azure Resource Manager-sjablonen bieden geen ondersteuning voor het uploaden van toepassingspakketten. U toepassingspakketten alleen uploaden door de toepassing te implementeren via de Azure CLI.
* Zie [Welke serviceplannen biedt Azure Spring Cloud voor](#what-service-plans-does-azure-spring-cloud-offer)meer informatie over quotabeperkingen?

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Hoe kan ik feedback geven en problemen melden?

Als u problemen ondervindt met Azure Spring Cloud, maakt u een [Azure Support Request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Als u een functieaanvraag wilt indienen of feedback wilt geven, gaat u naar [Azure Feedback](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Ontwikkeling

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Ik ben een Spring Cloud ontwikkelaar, maar nieuw voor Azure. Wat is de snelste manier voor mij om te leren hoe u een Azure Spring Cloud-toepassing ontwikkelen?

Volg de instructies in [Quickstart: Start een Azure Spring Cloud-toepassing met de Azure-portal](spring-cloud-quickstart-launch-app-portal.md)voor de snelste manier om aan de slag te gaan met Azure Spring Cloud.

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Welke Java-runtime ondersteunt Azure Spring Cloud?

Azure Spring Cloud ondersteunt Java 8 en 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Waar kan ik mijn Logboeken en statistieken van de Spring Cloud-applicatie bekijken?

Zoek statistieken op het tabblad App-overzicht en het tabblad [Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics)

Azure Spring Cloud ondersteunt het exporteren van Spring Cloud-toepassingslogboeken en -statistieken naar Azure Storage, EventHub en [Log Analytics.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries) De tabelnaam in Log Analytics is *AppPlatformLogsforSpring*. Zie [Diagnostische services](diagnostic-services.md)voor meer informatie over het inschakelen ervan.

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Ondersteunt Azure Spring Cloud gedistribueerde tracering?

Ja. Zie [Zelfstudie: Gedistribueerde tracering gebruiken met Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md)voor meer informatie.

### <a name="what-resource-types-does-service-binding-support"></a>Welke resourcetypen ondersteunt Service Binding?

Drie services worden momenteel ondersteund: Azure Cosmos DB, Azure Database voor MySQL en Azure Cache voor Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Kan ik permanente volumes vanuit mijn toepassingen bekijken, toevoegen of verplaatsen?

Ja.

## <a name="deployment"></a>Implementatie

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Ondersteunt Azure Spring Cloud blauwgroene implementatie?
Ja. Zie [Een faseringsomgeving instellen](spring-cloud-howto-staging-environment.md)voor meer informatie .

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Kan ik toegang krijgen tot Kubernetes om mijn toepassingscontainers te manipuleren?

Nee.  Azure Spring Cloud abstraheert de ontwikkelaar van de onderliggende architectuur, zodat u zich concentreren op toepassingscode en bedrijfslogica.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Ondersteunt Azure Spring Cloud het bouwen van containers vanaf de bron?

Ja. Zie [Uw Spring Cloud-toepassing starten vanaf de broncode](spring-cloud-launch-from-source.md)voor meer informatie.

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Ondersteunt Azure Spring Cloud automatisch schalen in app-exemplaren?

Nee.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Wat zijn de aanbevolen procedures voor het migreren van bestaande Spring Cloud-microservices naar Azure Spring Cloud?

Terwijl u bestaande Spring Cloud-microservices migreert naar Azure Spring Cloud, is het een goed idee om de volgende aanbevolen procedures in acht te nemen:
* Alle toepassingsafhankelijkheden moeten worden opgelost.
* Bereid uw configuratievermeldingen, omgevingsvariabelen en JVM-parameters voor, zodat u ze vergelijken met de implementatie in Azure Spring Cloud.
* Als u Service Binding wilt gebruiken, gaat u door uw Azure-services en zorgt u ervoor dat u de juiste toegangsmachtigingen hebt ingesteld.
* We raden u aan ingesloten services te verwijderen of uit te schakelen die mogelijk in strijd zijn met services die worden beheerd door Azure Spring Cloud, zoals onze Service Discovery-service, Config Server, enzovoort.
* Wij raden u aan gebruik te maken van officiële, stabiele Pivotal Spring bibliotheken. Onofficiële, bèta- of gevorkte versies van Pivotal Spring-bibliotheken hebben geen SLA-ondersteuning (Service-Level Agreement).

Controleer na de migratie uw CPU/RAM-statistieken en netwerkverkeer om ervoor te zorgen dat de toepassingsinstanties op de juiste manier worden geschaald.

## <a name="next-steps"></a>Volgende stappen

Als u nog meer vragen hebt, raadpleegt u de [handleiding voor probleemoplossing azure spring cloud.](spring-cloud-troubleshoot.md)
