---
title: Azure Security Center-incidenten - slimme correlaties van waarschuwingen
description: In dit onderwerp wordt uitgelegd hoe fusion gebruik maakt van slimme waarschuwingscorrelatie in de cloud om beveiligingsincidenten te genereren in Azure Security Center.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73686489"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Correlatie met slimme waarschuwingen in de cloud in Azure Security Center (incidenten)

Azure Security Center analyseert voortdurend hybride cloudworkloads met behulp van geavanceerde analyses en bedreigingsinformatie om u te waarschuwen voor schadelijke activiteiten.

De breedte van de dreiging dekking groeit. De noodzaak om zelfs het geringste compromis te detecteren is belangrijk, en het kan een uitdaging zijn voor beveiligingsanalisten om de verschillende waarschuwingen te triageen en een daadwerkelijke aanval te identificeren. Security Center helpt analisten omgaan met deze alert vermoeidheid. Het helpt bij het diagnosticeren van aanvallen wanneer ze zich voordoen, door verschillende waarschuwingen en low fidelity signalen te correleren in beveiligingsincidenten.

Fusion analytics is de technologie en analytische back-end die security center-incidenten aandrijft, waardoor het verschillende waarschuwingen en contextuele signalen samen kan correleren. Fusion kijkt naar de verschillende signalen gemeld op een abonnement over de middelen. Fusion vindt patronen die aanvalsprogressie of signalen onthullen met gedeelde contextuele informatie, wat aangeeft dat u een uniforme responsprocedure voor hen moet gebruiken.

Fusion analytics combineert kennis van beveiligingsdomeinen met AI om waarschuwingen te analyseren en nieuwe aanvalspatronen te ontdekken wanneer deze zich voordoen. 

Security Center maakt gebruik van MITRE Attack Matrix om waarschuwingen te associëren met hun vermeende intentie, waardoor de kennis van beveiligingsdomeinwordt geformaliseren. Bovendien kan Security Center met behulp van de verzamelde informatie voor elke stap van een aanval activiteiten uitsluiten die stappen van een aanval lijken te zijn, maar dat is het eigenlijk niet.

Omdat aanvallen vaak voorkomen bij verschillende tenants, kan Security Center AI-algoritmen combineren om aanvalssequenties te analyseren die op elk abonnement worden gerapporteerd. Deze techniek identificeert de aanvalssequenties als heersende waarschuwingspatronen, in plaats van alleen maar met elkaar geassocieerd te worden.

Tijdens een onderzoek naar een incident hebben analisten vaak extra context nodig om tot een oordeel te komen over de aard van de dreiging en hoe deze te beperken. Zelfs wanneer bijvoorbeeld een netwerkanomalie wordt gedetecteerd, zonder te begrijpen wat er nog meer gebeurt op het netwerk of met betrekking tot de beoogde bron, is het moeilijk te begrijpen welke acties u vervolgens moet uitvoeren. Om u te helpen, kan een beveiligingsincident artefacten, gerelateerde gebeurtenissen en informatie bevatten. De aanvullende informatie die beschikbaar is voor beveiligingsincidenten varieert, afhankelijk van het type bedreiging dat is gedetecteerd en de configuratie van uw omgeving. 

![Schermafbeelding van het gedetecteerde rapport Beveiligingsincident](./media/security-center-alerts-cloud-smart/security-incident.png)

Zie Hoe om te gaan met beveiligingsincidenten in Azure Security Center om beter inzicht te krijgen [in beveiligingsincidenten.](https://docs.microsoft.com/azure/security-center/security-center-incident)

