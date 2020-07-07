---
title: 'Azure Security Center incidenten: slimme correlaties van waarschuwingen'
description: In dit onderwerp wordt uitgelegd hoe fusie gebruikmaakt van Smart alert-correlatie voor het genereren van beveiligings incidenten in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: b26f0bab073ce248ca23bb8a815fa3e293ddba51
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "73686489"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Azure Security Center voor de correlatie van de cloud in een andere (incidenten)

Azure Security Center analyseert voortdurend hybride Cloud werkbelastingen door gebruik te maken van geavanceerde analyses en bedreigings informatie om u te waarschuwen over schadelijke activiteiten.

De omvang van de bedreigings dekking groeit. De nood zaak om te detecteren zelfs het zeer lichte risico is belang rijk en het kan lastig zijn voor beveiligings analisten om de verschillende waarschuwingen te sorteren en een echte aanval te identificeren. Security Center helpt analisten bij het bezorgen van deze waarschuwings vermoeidheid. Het helpt bij het vaststellen van aanvallen wanneer deze optreden, door verschillende waarschuwingen en signalen met lage betrouw baarheid in beveiligings incidenten te correleren.

Fusion Analytics is de technologie en analytische back-end die Security Center incidenten overbrengt, waardoor het mogelijk is om verschillende waarschuwingen en contextuele signalen samen te correleren. Fusie bekijkt de verschillende signalen die zijn gerapporteerd voor een abonnement over de resources. Met Fusion vindt u patronen die de voortgang van aanvallen onthullen of signalen met gedeelde contextuele informatie, waarmee wordt aangegeven dat u een uniforme antwoord procedure moet gebruiken.

Fusion Analytics combineert de kennis van het beveiligings domein met AI om waarschuwingen te analyseren, waarbij nieuwe aanvals patronen worden gedetecteerd zodra deze optreden. 

Security Center maakt gebruik van MITRE-aanvals matrix om waarschuwingen te koppelen aan hun waargenomen intentie, waardoor de kennis van de beveiliging van het domein wordt formaliseren. Daarnaast kunt u met behulp van de informatie die is verzameld voor elke stap van een aanval, Security Center activiteiten uitvoeren die de stappen van een aanval weer gegeven, maar eigenlijk niet.

Omdat aanvallen vaak voor verschillende tenants optreden, kan Security Center AI-algoritmen combi neren voor het analyseren van aanvals reeksen die op elk abonnement worden gerapporteerd. Deze techniek identificeert de aanvals reeksen als bekende waarschuwings patronen, in plaats van alleen incidenteel te koppelen aan elkaar.

Tijdens een onderzoek naar een incident hebben analisten vaak extra context nodig om een verdict te bereiken over de aard van de dreiging en hoe de oplossing kan worden verholpen. Als er bijvoorbeeld een afwijkend netwerk wordt gedetecteerd, zonder te begrijpen wat er gebeurt op het netwerk of met betrekking tot de doel resource, is het moeilijk te weten welke acties u vervolgens moet uitvoeren. Om u te helpen, kan een beveiligings incident artefacten, gerelateerde gebeurtenissen en informatie bevatten. De extra informatie die beschikbaar is voor beveiligings incidenten varieert, afhankelijk van het type bedreiging dat is gedetecteerd en de configuratie van uw omgeving. 

![Scherm afbeelding van gedetecteerd rapport van beveiligings incident](./media/security-center-alerts-cloud-smart/security-incident.png)

Zie [beveiligings incidenten afhandelen in azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-incident)voor meer informatie over beveiligings incidenten.

