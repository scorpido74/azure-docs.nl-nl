---
title: Slimme detectie-degradatie van de verhouding van de ernst van de tracering in Azure-toepassing inzichten | Microsoft Docs
description: Bewaak toepassings traceringen met Azure-toepassing inzichten voor ongebruikelijke patronen in telemetrie traceren.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/27/2017
ms.openlocfilehash: 83c1296beabaaae78289a653c6b30f6665f725c2
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820541"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Degradatie van de verhouding van de ernst van de tracering (preview-versie)

Traceringen worden veel gebruikt in toepassingen, omdat ze het verhaal van wat er achter de schermen gebeurt. Wanneer er iets mis gaat, bieden traceringen cruciaal inzicht in de volg orde van gebeurtenissen waardoor de status niet kan worden gewenst. Terwijl traceringen over het algemeen niet worden gestructureerd, is er een ding die u op concrete wijze kunt onderscheiden: hun ernst niveau. In de stationaire status van een toepassing verwachten we dat de verhouding tussen ' goede ' traceringen (*info* en *uitgebreid*) en ' slechte ' traceringen (*waarschuwing*, *fout*en *kritiek*) stabiel blijft. De veronderstelling is dat "slechte" traceringen regel matig op een bepaald gebied worden uitgevoerd als gevolg van een aantal redenen (tijdelijke netwerk problemen voor instantie). Maar wanneer een echt probleem groeit, wordt het doorgaans als een verhoging van het relatieve aandeel van ' slechte ' traceringen versus ' goede ' traceringen genoemd. Application Insights Slimme detectie analyseert automatisch de traceringen die door uw toepassing worden vastgelegd en kunnen u een waarschuwing krijgen over ongebruikelijke patronen in de ernst van de telemetrie van de tracering.

Voor deze functie is geen speciale configuratie vereist, behalve het configureren van traceer logboek registratie voor uw app (zie een listener voor traceer logboeken configureren voor [.net](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) of [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). Het is actief wanneer uw app voldoende uitzonderings-telemetrie genereert.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Wanneer kan ik dit type Smart Detection-melding krijgen?
Dit type melding kan worden weer gegeven als de verhouding tussen ' goede ' traceringen (traceringen die zijn geregistreerd met een niveau van *info* of *uitgebreid*) en ' slechte ' traceringen (traceringen die zijn vastgelegd met een *waarschuwings*niveau, *fout*of *onherstelbare*) worden verminderd met een specifieke dag, vergeleken met de basis lijn die is berekend over de afgelopen zeven dagen.

## <a name="does-my-app-definitely-have-a-problem"></a>Heeft mijn app een probleem?
Nee, een melding betekent niet dat uw app een probleem heeft. Hoewel een vermindering van de verhouding tussen ' goede ' en ' slechte ' traceringen kan duiden op een toepassings probleem, kan deze wijziging in verhouding mogelijk onschadelijk zijn. De toename kan bijvoorbeeld worden veroorzaakt door een nieuwe stroom in de toepassing die meer ' slechte ' traceringen dan bestaande stromen verzendt.

## <a name="how-do-i-fix-it"></a>Hoe kan ik oplossen?
De meldingen bevatten diagnostische gegevens voor ondersteuning bij het diagnose proces:
1. **Sorteren.** In de melding ziet u hoeveel bewerkingen worden beïnvloed. Dit kan handig zijn bij het toewijzen van een prioriteit aan het probleem.
2. **Ligt.** Is het probleem van invloed op al het verkeer of alleen een bepaalde bewerking? Deze informatie kan worden opgehaald uit de melding.
3. **Vaststellen.** U kunt de gerelateerde items en rapporten koppelen aan ondersteunende informatie, om u te helpen bij het oplossen van het probleem.


