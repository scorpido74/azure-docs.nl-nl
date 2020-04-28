---
title: Veelgestelde vragen over Azure lente Cloud | Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure veer Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 95260d9a15fdc32c9fddccbcf63ae9fa564fd36a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176767"
---
# <a name="azure-spring-cloud-faq"></a>Veelgestelde vragen over Azure veer Cloud

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure veer Cloud.

## <a name="general"></a>Algemeen

### <a name="why-azure-spring-cloud"></a>Waarom Azure lente Cloud?

Azure lente Cloud biedt een platform als een service (PaaS) voor veer-Cloud ontwikkelaars. Azure lente-Cloud beheert uw toepassings infrastructuur zodat u zich kunt concentreren op toepassings code en bedrijfs logica. De belangrijkste functies die zijn ingebouwd in azure lente Cloud zijn onder andere Eureka, configuratie server, service register server, Pivotal build service, Blue-groen implementaties en meer. Met deze service kunnen ontwikkel aars ook hun toepassingen koppelen aan andere Azure-Services, zoals Azure Cosmos DB, Azure Database for MySQL en Azure cache voor redis.

Azure veer Cloud verbetert de diagnostische ervaring van toepassingen voor ontwikkel aars en Opera tors door Azure Monitor, Application Insights en Log Analytics te integreren.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Welke service plannen biedt Azure lente-Cloud aanbieding?

Azure lente-Cloud biedt een service abonnement tijdens de preview-periode.  Een lente-Cloud implementatie bevat 16 vCPU-kernen en 32 gigabytes (GB) aan geheugen.  De bovengrens voor elk micro service-exemplaar binnen een implementatie is 4 vCPU-kernen met 8 GB geheugen.

Resource | Aantal
------- | -------
App-exemplaren per lente toepassing | 20
Totaal aantal app-exemplaren per Azure veer Cloud service-exemplaar | 500
Azure veer Cloud service-instanties per regio per abonnement | 10
Permanente volumes | 10 x 50 GBytes

\*_Als u de limiet wilt verhogen, opent u een [ondersteunings ticket](https://azure.microsoft.com/support/faq/)._

Zie [Veelgestelde vragen over Azure-ondersteuning](https://azure.microsoft.com/support/faq/)voor meer informatie.

### <a name="how-secure-is-azure-spring-cloud"></a>Hoe veilig is Azure lente-Cloud?

Beveiliging en privacy zijn een van de belangrijkste prioriteiten voor Azure-klanten en Azure lente-Cloud gebruikers. Azure zorgt ervoor dat alleen klanten toegang hebben tot toepassings gegevens,-Logboeken of-configuraties door al deze gegevens veilig te versleutelen. Alle service-exemplaren in azure lente Cloud zijn van elkaar geïsoleerd.

Azure lente Cloud biedt volledige TLS/SSL-en certificaat beheer.

Essentiële beveiligings patches voor OpenJDK en lente-Cloud Runtimes worden zo snel mogelijk toegepast op Azure veer Cloud.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>In welke regio's is Azure lente-Cloud beschikbaar?

VS-Oost, VS-West 2, Europa-west en Zuidoost-Azië.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Wat zijn de bekende beperkingen van Azure lente-Cloud?

Tijdens de preview-versie heeft Azure lente Cloud de volgende bekende beperkingen:

* `spring.application.name`wordt overschreven door de naam van de toepassing die wordt gebruikt voor het maken van elke toepassing.
* `server.port`is niet toegestaan in het configuratie bestand van de Git-opslag plaats. Als u het toevoegt aan het configuratie bestand, wordt uw toepassing waarschijnlijk onbereikbaar van andere toepassingen of Internet.
* De Azure Portal-en Azure Resource Manager-sjablonen bieden geen ondersteuning voor het uploaden van toepassings pakketten. U kunt toepassings pakketten alleen uploaden door de toepassing te implementeren via de Azure CLI.
* Als u meer wilt weten over quotum beperkingen, raadpleegt u [welke service plannen Azure lente Cloud offers?](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Hoe kan ik feedback geven en problemen melden?

Als u problemen ondervindt met Azure lente Cloud, maakt u een [ondersteunings aanvraag voor Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Als u een functie aanvraag wilt verzenden of feedback wilt geven, gaat u naar [Azure feedback](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Ontwikkeling

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Ik ben een lente-Cloud ontwikkelaar, maar er is nog geen ervaring met Azure. Wat is de snelste manier om te leren hoe u een Azure lente-Cloud toepassing kunt ontwikkelen?

Voor de snelste manier om aan de slag te gaan met Azure lente Cloud, volgt u de instructies in [Quick Start: een Azure lente-Cloud toepassing starten met behulp van de Azure Portal](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Wat Java runtime ondersteunt Azure lente-Cloud ondersteuning?

Azure lente-Cloud ondersteunt Java 8 en 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Waar kan ik de logboeken en metrische gegevens van mijn lente-Cloud weer geven?

Zoek metrische gegevens op het tabblad Overzicht van de app en het tabblad [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) .

Azure lente Cloud ondersteunt het exporteren van Lente-Cloud toepassings logboeken en-metrische gegevens naar Azure Storage, EventHub en [log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). De tabel naam in Log Analytics is *AppPlatformLogsforSpring*. Zie [diagnostische services](diagnostic-services.md)voor meer informatie over het inschakelen van deze functie.

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Wordt gedistribueerde tracering door Azure lente Cloud ondersteund?

Ja. Zie [zelf studie: gedistribueerde tracering gebruiken met Azure lente-Cloud](spring-cloud-tutorial-distributed-tracing.md)voor meer informatie.

### <a name="what-resource-types-does-service-binding-support"></a>Welke resource typen worden ondersteund door service binding?

Er worden momenteel drie services ondersteund: Azure Cosmos DB, Azure Database for MySQL en Azure cache voor redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Kan ik permanente volumes weer geven, toevoegen of verplaatsen vanuit mijn toepassingen?

Ja.

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>Wanneer ik een Azure lente-Cloud service-exemplaar Verwijder/Verplaats, worden ook de bijbehorende uitbreidings resources verwijderd/verplaatst?

Het hangt af van de logica van resource providers waarbij de uitbreidings bronnen horen. De extensie resources van een `Microsoft.AppPlatform` exemplaar horen niet bij dezelfde naam ruimte, waardoor het gedrag afhankelijk is van verschillende resource providers. De bewerking verwijderen/verplaatsen getrapt bijvoorbeeld niet in de resources met **Diagnostische instellingen** . Als er een nieuw Azure-exemplaar voor de Cloud wordt ingericht met dezelfde resource-ID als de verwijderde, of als het vorige exemplaar van de Azure veer Cloud wordt teruggezet, worden de vorige bronnen voor **Diagnostische gegevens** nog verder uitgebreid.

## <a name="deployment"></a>Implementatie

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Biedt Azure lente Cloud ondersteuning voor blauw-groen-implementatie?
Ja. Zie [een faserings omgeving instellen](spring-cloud-howto-staging-environment.md)voor meer informatie.

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Kan ik Kubernetes gebruiken om mijn toepassings containers te bewerken?

Nee.  Azure lente-Cloud is een samen vatting van de ontwikkelaar van de onderliggende architectuur, zodat u zich kunt concentreren op toepassings code en bedrijfs logica.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Biedt Azure lente Cloud ondersteuning voor het bouwen van containers van de bron?

Ja. Zie [de lente Cloud toepassing vanuit de bron code starten](spring-cloud-launch-from-source.md)voor meer informatie.

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Biedt Azure lente Cloud ondersteuning voor automatisch schalen in app-exemplaren?

Nee.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Wat zijn de aanbevolen procedures voor het migreren van bestaande lente-Cloud micro Services naar Azure veer Cloud?

Wanneer u bestaande lente-Cloud micro Services migreert naar Azure lente-Cloud, is het een goed idee om de volgende best practices te bekijken:
* Alle toepassings afhankelijkheden moeten worden opgelost.
* Bereid uw configuratie vermeldingen, omgevings variabelen en JVM-para meters voor, zodat u ze kunt vergelijken met de implementatie in azure lente Cloud.
* Als u de service binding wilt gebruiken, gaat u door uw Azure-Services en zorgt u ervoor dat u de juiste toegangs machtigingen hebt ingesteld.
* U wordt aangeraden alle Inge sloten services te verwijderen of uit te scha kelen die mogelijk conflicteren met services die worden beheerd door Azure lente Cloud, zoals onze service discovery-service, configuratie server, enzovoort.
* U wordt aangeraden officiële, stabiele Pivot-veer bibliotheken te gebruiken. Niet-officiële, bèta-of gevorkeerde versies van Pivot-lente bibliotheken hebben geen SLA-ondersteuning (Service Level Agreement).

Controleer na de migratie de CPU/RAM-metrische gegevens en het netwerk verkeer om ervoor te zorgen dat de toepassings exemplaren op de juiste wijze worden geschaald.

## <a name="next-steps"></a>Volgende stappen

Als u meer vragen hebt, raadpleegt u de [Azure lente-probleemoplossings gids voor Clouds](spring-cloud-troubleshoot.md).
