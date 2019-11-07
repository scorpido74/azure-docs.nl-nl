---
title: Veelgestelde vragen over Azure lente Cloud | Microsoft Docs
description: Raadpleeg de veelgestelde vragen over Azure lente Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 1fb241ab1335e6b9c9be55acbbe2e21378831b69
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607232"
---
# <a name="frequently-asked-questions"></a>Veelgestelde vragen

In dit artikel worden veelgestelde vragen over Azure lente Cloud behandeld. 

## <a name="general"></a>Algemeen

### <a name="why-azure-spring-cloud"></a>Waarom Azure lente Cloud?

Azure lente Cloud biedt een platform als een service (PaaS) voor veer aars. Azure lente-Cloud beheert uw toepassings infrastructuur zodat u zich kunt concentreren op toepassings code en bedrijfs logica. Belangrijkste functies die zijn ingebouwd in azure lente Cloud, zijn onder andere Eureka, configuratie server, service register server, Pivotal build service, Blue-groen implementaties en meer. Met deze service kunnen ontwikkel aars ook hun toepassingen koppelen aan Azure-Services, zoals CosmosDB, MySQL en Azure cache voor redis.

Azure lente Cloud verbetert de diagnostische ervaring van toepassingen voor ontwikkel aars en Opera tors door Azure Monitor, Application Insights en Log Analytics te integreren.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Welke service plannen biedt Azure lente-Cloud aanbieding?

Azure lente-Cloud biedt een service abonnement tijdens de preview-periode.  Een lente-Cloud implementatie bevat 16 vCPU-kernen en 32 GB geheugen.  De bovengrens voor elk micro service-exemplaar binnen een implementatie is 4 vCPU-kernen met 8 GB geheugen.

Resource | Aantal
------- | -------
App-exemplaren per lente toepassing | 20
Totaal aantal app-exemplaren per Azure veer Cloud service-exemplaar | 50 *
Azure veer Cloud service-instanties per regio per abonnement | twee
Permanente volumes | 10 x 50 GBytes

*_een [ondersteunings ticket](https://azure.microsoft.com/support/faq/) te openen om de limiet te verhogen._

Raadpleeg [Veelgestelde vragen over ondersteuning voor Azure](https://azure.microsoft.com/support/faq/)voor meer informatie.

### <a name="how-secure-is-azure-spring-cloud"></a>Hoe veilig is Azure lente-Cloud?

Beveiliging en privacy zijn een van de belangrijkste prioriteiten voor Azure-klanten en Azure lente-Cloud gebruikers. Azure zorgt ervoor dat alleen de klant toegang heeft tot toepassings gegevens,-Logboeken of-configuraties door al deze gegevens veilig te versleutelen. Alle service-exemplaren in azure lente Cloud zijn van elkaar geïsoleerd.

Azure lente Cloud biedt volledige SSL-en certificaat beheer.

Essentiële beveiligings patches voor OpenJDK en lente-Cloud Runtimes worden zo snel mogelijk toegepast op Azure veer Cloud.

### <a name="which-regions-azure-spring-cloud-are-available"></a>Welke regio's zijn er Azure lente-Cloud beschikbaar?

VS-Oost, VS-West 2, Europa-west en Zuidoost-Azië.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Wat zijn de bekende beperkingen van Azure lente-Cloud

Dit zijn de bekende beperkingen van Azure lente Cloud terwijl de service in de preview-versie is.

* `spring.application.name` wordt overschreven door de naam van de toepassing die wordt gebruikt voor het maken van elke toepassing.
* `server.port` is niet toegestaan in het configuratie bestand van Git opslag plaats. Als u het toevoegt aan het configuratie bestand, zal uw toepassing waarschijnlijk niet bereikbaar zijn vanuit andere toepassingen of Internet.
* De Azure Portal-en Resource Manager-sjablonen bieden geen ondersteuning voor het uploaden van toepassings pakketten. Dit kan alleen worden gedaan via toepassings implementatie door Azure CLI.
* Raadpleeg voor meer informatie over quotum beperkingen de [Azure lente-Cloud aanbieding voor service plannen](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Hoe kan ik feedback geven en problemen melden?

Als u problemen ondervindt met Azure lente Cloud, moet u een [Azure-ondersteunings aanvraag](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)maken. Voor functie aanvragen gaat u naar [Azure feedback](https://feedback.azure.com/forums/34192--general-feedback) om functies aan te vragen of om feedback te geven.

## <a name="development"></a>Ontwikkeling

### <a name="i-am-a-spring-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-aazure-spring-cloud-application"></a>Ik ben een lente ontwikkelaar, maar nieuw voor Azure, wat is de snelste manier om te leren hoe ik gereserveerde lente-Cloud toepassing kan ontwikkelen?

De snelste manier om aan de slag te gaan met Azure lente Cloud is door [deze Snelstartgids](spring-cloud-quickstart-launch-app-portal.md)te volgen.

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Wat Java runtime ondersteunt Azure lente-Cloud ondersteuning?

Azure lente-Cloud ondersteunt Java 8 en 11.

### <a name="where-can-i-see-my-spring-application-logs-and-metrics"></a>Waar kan ik mijn veer toepassings logboeken en-metrische gegevens zien?

Zoek metrische gegevens op het tabblad Overzicht van de app en het tabblad [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) .

Azure lente-Cloud ondersteunt het exporteren van uw veer toepassings logboeken en-metrische gegevens naar Azure Storage, EventHub en [log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). De tabel naam in Log Analytics is `AppPlatformLogsforSpring`. Lees dit artikel over onze [diagnostische services](diagnostic-services.md)om het in te scha kelen.

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Wordt gedistribueerde tracering door Azure lente Cloud ondersteund?

Ja, ga naar [gedistribueerde tracering](spring-cloud-tutorial-distributed-tracing.md) voor meer informatie.

### <a name="what-resource-types-does-service-binding-support"></a>Welke resource typen worden ondersteund door service binding?

Er worden momenteel drie services ondersteund: Azure Cosmos DB, Azure Database for MySQL en Azure cache voor redis.

### <a name="can-i-viewaddmove-persistent-volumes-from-inside-my-applications"></a>Kan ik permanente volumes weer geven/toevoegen/verplaatsen vanuit mijn toepassingen?
Ja.

## <a name="deployment"></a>Implementatie

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Biedt Azure lente Cloud ondersteuning voor blauw-groen-implementatie?
Ja, ga naar de [gids voor faserings omgeving](spring-cloud-howto-staging-environment.md) voor meer informatie.

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Kan ik Kubernetes gebruiken om mijn toepassings containers te bewerken?

Nee.  Azure lente-Cloud is een samen vatting van de ontwikkelaar van de onderliggende architectuur, zodat u zich kunt concentreren op toepassings code en bedrijfs logica.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Biedt Azure lente Cloud ondersteuning voor het bouwen van containers van de bron?

Ja, ga naar [implementeren vanuit de bron](spring-cloud-launch-from-source.md) voor meer informatie.

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Biedt Azure lente Cloud ondersteuning voor automatisch schalen in app-exemplaren?

Nee.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-microservices-to-azure-spring-cloud"></a>Wat zijn de aanbevolen procedures voor het migreren van bestaande lente-micro Services naar Azure lente-Cloud?

Voordat u bestaande Spring micro Services migreert naar Azure lente-Cloud,
* Alle toepassings afhankelijkheden moeten worden opgelost.
* Bereid uw configuratie vermeldingen, omgevings variabelen en JVM-para meters voor, zodat u ze kunt vergelijken met de implementatie in azure lente Cloud.
* Als u de service binding wilt gebruiken, gaat u door uw Azure-Services en zorgt u ervoor dat u de juiste toegangs machtigingen hebt ingesteld.
* We raden u aan om Inge sloten services te verwijderen of uit te scha kelen die mogelijk conflicteren met services die worden beheerd door Azure lente Cloud, zoals onze service discovery-service, configuratie server, enzovoort.
*-* U wordt aangeraden officiële en stabiele Pivot-veer bibliotheken te gebruiken. Niet-officiële, bèta-of gevorkte versies van Pivot-lente bibliotheken hebben geen SLA-ondersteuning.

Controleer na de migratie de CPU/RAM-metrische gegevens en het netwerk verkeer om te controleren of de toepassings exemplaren op de juiste wijze zijn geschaald.

## <a name="next-steps"></a>Volgende stappen

[Raadpleeg de hand leiding voor het oplossen van problemen als u meer vragen hebt](spring-cloud-troubleshoot.md).