---
title: Containerbeveiliging in Azure Security Center | Microsoft Documenten
description: Meer informatie over de containerbeveiligingsfuncties van Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: d46e2a9820ec0c45d197f135428f1ace712b2fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125148"
---
# <a name="container-security-in-security-center"></a>Containerbeveiliging in Security Center

Azure Security Center is de Azure-native oplossing voor containerbeveiliging. Security Center is ook de optimale glas-in-een-opslag voor de beveiliging van uw cloudworkloads, VM's, servers en containers.

In dit artikel wordt beschreven hoe U met het Beveiligingscentrum de beveiliging van uw containers en hun apps verbeteren, bewaken en onderhouden. U leert hoe Security Center helpt bij deze kernaspecten van containerbeveiliging:

* Kwetsbaarheidsbeheer
* Verharding van de omgeving van de container
* Runtime-beveiliging

[![Tabblad containerbeveiliging van Azure Security Center](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Zie De beveiliging van uw [containers controleren](monitor-container-security.md)voor instructies over het gebruik van deze functies.

## <a name="vulnerability-management---scanning-container-images"></a>Kwetsbaarheidsbeheer - containerafbeeldingen scannen
Als u uw Azure Container Registry op basis van ARM wilt controleren, controleert u of u zich op de standaardlaag van het Beveiligingscentrum bevindt (zie [prijzen).](/azure/security-center/security-center-pricing) Schakel vervolgens de optionele containerregistersbundel in. Wanneer een nieuwe afbeelding wordt geduwd, scant Security Center de afbeelding met behulp van een scanner van de toonaangevende leverancier van kwetsbaarhedenscannen, Qualys.

Wanneer er problemen worden gevonden - door Qualys of Security Center - ontvangt u een melding in het dashboard van het Beveiligingscentrum. Voor elk beveiligingslek biedt Security Center bruikbare aanbevelingen, samen met een ernstclassificatie en richtlijnen voor het oplossen van het probleem. Zie de [referentielijst met aanbevelingen](recommendations-reference.md#recs-containers)voor meer informatie over de aanbevelingen van Security Center voor containers.

Security Center filtert en classificert bevindingen van de scanner. Wanneer een afbeelding in orde is, markeert Security Center deze als zodanig. Security Center genereert alleen beveiligingsaanbevelingen voor afbeeldingen die problemen moeten oplossen. Door alleen te melden wanneer er problemen zijn, vermindert Security Center de kans op ongewenste informatieve waarschuwingen.

## <a name="environment-hardening"></a>Verharding van het milieu

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Continue bewaking van uw Docker-configuratie
Azure Security Center identificeert onbeheerde containers die worden gehost op IaaS Linux VM's of andere Linux-machines waarop Docker-containers worden uitgevoerd. Security Center beoordeelt continu de configuraties van deze containers. Het vergelijkt ze vervolgens met het [Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/).

Security Center bevat de volledige regelset van de CIS Docker Benchmark en waarschuwt u als uw containers niet voldoen aan een van de besturingselementen. Wanneer het verkeerde configuraties vindt, genereert Security Center beveiligingsaanbevelingen. Gebruik de **aanbevelingenpagina** om aanbevelingen weer te geven en problemen op te lossen. U ziet ook de aanbevelingen op het tabblad **Containers** waarop alle virtuele machines worden weergegeven die met Docker zijn geïmplementeerd. 

Zie het [containergedeelte](recommendations-reference.md#recs-containers) van de verwijzingstabel aanbevelingen voor deze functie voor meer informatie over de relevante aanbevelingen voor het beveiligingscentrum die voor deze functie kunnen worden weergegeven.

Wanneer u de beveiligingsproblemen van een vm verkent, biedt beveiligingscentrum aanvullende informatie over de containers op de machine. Dergelijke informatie omvat de Docker-versie en het aantal afbeeldingen dat op de host wordt uitgevoerd. 

>[!NOTE]
> Deze CIS-benchmarkcontroles worden niet uitgevoerd op door AKS beheerde exemplaren of door Databricks beheerde VM's.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Continue monitoring van uw Kubernetes clusters
Security Center werkt samen met Azure Kubernetes Service (AKS), microsoft's managed container orchestration service voor het ontwikkelen, implementeren en beheren van gecontaineriseerde toepassingen.

AKS biedt beveiligingscontroles en inzicht in de beveiligingshouding van uw clusters. Security Center gebruikt deze functies om:
* Voortdurend de configuratie van uw AKS-clusters bewaken
* Beveiligingsaanbevelingen genereren die zijn afgestemd op de industrienormen

Zie het [containergedeelte](recommendations-reference.md#recs-containers) van de verwijzingstabel aanbevelingen voor deze functie voor meer informatie over de relevante aanbevelingen voor het beveiligingscentrum die voor deze functie kunnen worden weergegeven.

## <a name="run-time-protection---real-time-threat-detection"></a>Run-time bescherming - Real-time bedreigingsdetectie

Security Center biedt real-time detectie van bedreigingen voor uw gecontaineriseerde omgevingen en genereert waarschuwingen voor verdachte activiteiten. U kunt deze informatie gebruiken om snel beveiligingsproblemen op te lossen en om de beveiliging van uw containers te verbeteren.

We detecteren bedreigingen op host- en AKS-clusterniveau. Zie [bedreigingsdetectie voor Azure-containers voor](threat-protection.md#azure-containers)meer informatie.


## <a name="container-security-faq"></a>Veelgestelde vragen over containerbeveiliging

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Welke typen afbeeldingen kunnen Azure Security Center scannen?
Security Center scant Op Linux OS gebaseerde afbeeldingen die shell-toegang bieden. 

De Qualys-scanner ondersteunt geen superminimalistische afbeeldingen zoals [Docker-krasafbeeldingen](https://hub.docker.com/_/scratch/) of 'Distroless'-afbeeldingen die alleen uw toepassing en de gebruiksafhankelijkheden bevatten zonder een package manager, shell of OS.

### <a name="how-does-azure-security-center-scan-an-image"></a>Hoe scant Azure Security Center een afbeelding?
De afbeelding wordt uit het register gehaald. Het wordt vervolgens uitgevoerd in een geïsoleerde sandbox met de Qualys-scanner die een lijst met bekende kwetsbaarheden uitzoekt.

Security Center filtert en classificert bevindingen van de scanner. Wanneer een afbeelding in orde is, markeert Security Center deze als zodanig. Security Center genereert alleen beveiligingsaanbevelingen voor afbeeldingen die problemen moeten oplossen. Door alleen te melden wanneer er problemen zijn, vermindert Security Center de kans op ongewenste informatieve waarschuwingen.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Hoe vaak scant Azure Security Center mijn afbeeldingen?
Beeldscans worden geactiveerd op elke push.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Kan ik de scanresultaten krijgen via REST API?
Ja. De resultaten vallen onder [Sub-Assessments Rest API](/rest/api/securitycenter/subassessments/list/). U ook Azure Resource Graph (ARG) gebruiken, de Kusto-achtige API voor al uw resources: een query kan een specifieke scan ophalen.
 

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over containerbeveiliging in Azure Security Center:

* Zie het containergedeelte van Bescherm uw machines en [toepassingen](security-center-virtual-machine-protection.md#containers)om de beveiligingshouding van uw containergerelateerde resources te bekijken.

* Details van de [integratie met Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* Details van de [integratie met Azure Container Registry](azure-container-registry-integration.md)