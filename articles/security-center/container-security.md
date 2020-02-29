---
title: Beveiliging van de container in Azure Security Center | Microsoft Docs
description: Meer informatie over de beveiligings functies van de container van Azure Security Center.
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
ms.openlocfilehash: cd9907d041562d23bc487de319a17d36b68db6f3
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77916114"
---
# <a name="container-security-in-security-center"></a>Beveiliging van de container in Security Center

Azure Security Center is de Azure-systeem eigen oplossing voor container beveiliging. Security Center is ook de optimale glas ervaring voor de beveiliging van uw Cloud werkbelastingen, Vm's, servers en containers.

In dit artikel wordt beschreven hoe Security Center u helpt bij het verbeteren, bewaken en onderhouden van de beveiliging van uw containers en hun apps. U leert hoe Security Center helpt bij het werken met deze kern aspecten van container beveiliging:

* Beveiligings beheer
* Versterking van de omgeving van de container
* Runtime-beveiliging

[tabblad Beveiliging van de container ![Azure Security Center](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Zie [de beveiliging van uw containers bewaken](monitor-container-security.md)voor instructies over het gebruik van deze functies.

## <a name="vulnerability-management---scanning-container-images-preview"></a>Beveiligings beheer-container installatie kopieën scannen (preview)
Als u uw op ARM gebaseerde Azure Container Registry wilt bewaken, controleert u of u de Standard-laag van Security Center hebt (Zie de [prijzen](/azure/security-center/security-center-pricing)). Schakel vervolgens de optionele container registers bundel in. Wanneer een nieuwe installatie kopie wordt gepusht, wordt de installatie kopie door Security Center gescand met behulp van een scanner uit de toonaangevende beveiligings problemen voor het scannen van leveranciers, Qualys.

Als er problemen worden gevonden, wordt er een melding weer gegeven in het Security Center-dash board van Qualys of Security Center. Security Center voorziet in elk beveiligings probleem met actie-aanbevelingen, samen met een Ernst classificatie en richt lijnen voor het oplossen van het probleem. Zie de [naslag lijst met aanbevelingen](recommendations-reference.md#recs-containers)voor meer informatie over de aanbevelingen van Security Center voor containers.

## <a name="environment-hardening"></a>Omgevings beveiliging

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Doorlopende bewaking van uw docker-configuratie
Azure Security Center identificeert niet-beheerde containers die worden gehost op IaaS Linux-Vm's of andere Linux-machines waarop docker-containers worden uitgevoerd. Security Center doorlopend de configuraties van deze containers evalueren. Vervolgens worden ze vergeleken met de [CIS-Referentie (Center for Internet Security)](https://www.cisecurity.org/benchmark/docker/).

Security Center bevat de volledige regelset van de CIS docker-Bench Mark en waarschuwt u als uw containers niet voldoen aan een van de besturings elementen. Als er onjuiste configuraties worden gevonden, worden in Security Center beveiligings aanbevelingen gegenereerd. Op de **pagina aanbevelingen** kunt u aanbevelingen bekijken en problemen oplossen. U ziet ook de aanbevelingen op het tabblad **containers** waarin alle virtuele machines worden weer gegeven die zijn geïmplementeerd met docker. 

Zie de [sectie container](recommendations-reference.md#recs-containers) in de naslag tabel met aanbevelingen voor meer informatie over de relevante Security Center aanbevelingen die voor deze functie kunnen worden weer gegeven.

Wanneer u de beveiligings problemen van een virtuele machine wilt verkennen, bevat Security Center extra informatie over de containers op de computer. Deze informatie omvat de docker-versie en het aantal installatie kopieën die op de host worden uitgevoerd. 

>[!NOTE]
> Deze CIS-benchmark controles worden niet uitgevoerd op door AKS beheerde instanties of door Databricks beheerde Vm's.

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>Continue bewaking van uw Kubernetes-clusters (preview-versie)
Security Center werkt samen met Azure Kubernetes service (AKS), de beheerde container service van micro soft voor het ontwikkelen, implementeren en beheren van toepassingen in containers.

AKS biedt beveiligings controles en zicht baarheid in de beveiligings-postuur van uw clusters. Security Center gebruikt deze functies voor het volgende:
* De configuratie van uw AKS-clusters continu controleren
* Beveiligings aanbevelingen die zijn afgestemd op de industrie normen genereren

Zie de [sectie container](recommendations-reference.md#recs-containers) in de naslag tabel met aanbevelingen voor meer informatie over de relevante Security Center aanbevelingen die voor deze functie kunnen worden weer gegeven.

## <a name="run-time-protection---real-time-threat-protection"></a>Run-time beveiliging-real-time beveiliging van bedreigingen

Security Center biedt realtime bescherming van bedreigingen voor uw container omgevingen en genereert waarschuwingen voor verdachte activiteiten. U kunt deze informatie gebruiken om snel beveiligingsproblemen op te lossen en om de beveiliging van uw containers te verbeteren.

We detecteren bedreigingen op het niveau van de host en het AKS-cluster. Zie [bedreigings beveiliging voor Azure-containers](threat-protection.md#azure-containers)voor volledige informatie.


## <a name="container-security-faq"></a>Veelgestelde vragen over container beveiliging

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Welke typen installatie kopieën kunnen Azure Security Center scannen?
Security Center scant installatie kopieën op basis van Linux-besturings systemen die shell toegang bieden. 

De Qualys-scanner ondersteunt geen super minimale afbeeldings afbeeldingen zoals [docker](https://hub.docker.com/_/scratch/) -werk afbeeldingen, of Distroless-installatie kopieën die alleen uw toepassing en runtime-afhankelijkheden bevatten (zonder pakket beheer, shell of besturings systeem).

### <a name="how-does-we-scan-azure-security-center-scan-an-image"></a>Hoe scant het Azure Security Center een afbeelding scannen?
De installatie kopie wordt opgehaald uit het REGI ster. Het wordt vervolgens uitgevoerd in een geïsoleerde sandbox met de Qualys-scanner, waarmee een lijst met bekende beveiligings problemen wordt geëxtraheerd.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Hoe vaak Azure Security Center mijn afbeeldingen scannen?
Scans van afbeeldingen worden geactiveerd tijdens elke push.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Kan ik de scan resultaten verkrijgen via REST API?
Ja. De resultaten bevinden zich onder [Subevaluaties van de rest-API](/rest/api/securitycenter/subassessments/list/). Daarnaast kunt u Azure resource Graph (ARG) gebruiken, de Kusto-achtige API voor al uw resources: met een query kunt u een specifieke scan ophalen.
 

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over de beveiliging van de container in Azure Security Center raadpleegt u de volgende verwante artikelen:

* Zie de sectie containers van [uw computers en toepassingen beveiligen](security-center-virtual-machine-protection.md#containers)als u de beveiligings postuur van uw container bronnen wilt weer geven.

* Details van de [integratie met de Azure Kubernetes-service](azure-kubernetes-service-integration.md)

* Details van de [integratie met Azure container Registry](azure-container-registry-integration.md)