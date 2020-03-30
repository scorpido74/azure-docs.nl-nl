---
title: Geheugenlek detecteren - Smart Detection van Azure Application Insights
description: Monitor toepassingen met Azure Application Insights voor mogelijke geheugenlekken.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 85d138518dfb1313a810657016e9fe3143887b6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671695"
---
# <a name="memory-leak-detection-preview"></a>Geheugenlekdetectie (voorbeeld)

Application Insights analyseert automatisch het geheugenverbruik van elk proces in uw toepassing en kan u waarschuwen voor mogelijke geheugenlekken of een verhoogd geheugenverbruik.

Deze functie vereist geen speciale installatie, anders dan [het configureren van prestatiemeteritems](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) voor uw app. Deze is actief wanneer uw app voldoende geheugenprestatiemeteren telemetrie genereert (bijvoorbeeld Privébytes).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Wanneer zou ik dit soort slimme detectiemeldingen krijgen?
Een typische melding volgt een consistente toename van het geheugenverbruik over een lange periode, in een of meer processen en/of een of meer machines, die deel uitmaken van uw toepassing. Machine learning-algoritmen worden gebruikt voor het detecteren van een verhoogd geheugenverbruik dat overeenkomt met het patroon van een geheugenlek.

## <a name="does-my-app-really-have-a-problem"></a>Heeft mijn app echt een probleem?
Nee, een melding betekent niet dat uw app zeker een probleem heeft. Hoewel geheugenlekpatronen meestal wijzen op een toepassingsprobleem, kunnen deze patronen typisch zijn voor uw specifieke proces of een natuurlijke zakelijke rechtvaardiging hebben en kunnen worden genegeerd.

## <a name="how-do-i-fix-it"></a>Hoe kan ik dit probleem oplossen?
De meldingen bevatten diagnostische informatie ter ondersteuning van het diagnostische analyseproces:
1. **Triage.** De melding toont u de hoeveelheid geheugentoename (in GB) en het tijdsbereik waarin het geheugen is toegenomen. Dit kan u helpen een prioriteit toe te wijzen aan het probleem.
2. **Scope.** Hoeveel machines vertoonden het geheugenlekpatroon? Hoeveel uitzonderingen zijn geactiveerd tijdens het potentiële geheugenlek? Deze informatie kan worden verkregen uit de kennisgeving.
3. **Diagnosticeren.** De detectie bevat het geheugenlekpatroon, dat het geheugenverbruik van het proces in de loop van de tijd weergeeft. U ook de gerelateerde items en rapporten gebruiken die linken naar ondersteunende informatie, om u te helpen het probleem verder te diagnosticeren.
