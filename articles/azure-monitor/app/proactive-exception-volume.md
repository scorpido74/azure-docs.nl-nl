---
title: Abnormale stijging van het exceptionvolume - Azure Application Insights
description: Monitor toepassingsuitzonderingen met Slimme detectie in Azure Application Insights voor ongebruikelijke patronen in uitzonderingsvolume.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: a08fae4774a8afb9959f55ea3196cd1a45c33439
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671763"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Abnormale stijging van het uitzonderingsvolume (voorbeeld)

Application Insights analyseert automatisch de uitzonderingen die in uw toepassing worden gegooid en kan u waarschuwen voor ongebruikelijke patronen in uw uitzonderingstelemetrie.

Deze functie vereist geen speciale installatie, behalve [het configureren van uitzonderingsrapportage](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) voor uw app. Het is actief wanneer uw app voldoende uitzonderingtelemetrie genereert.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Wanneer zou ik dit soort slimme detectiemeldingen krijgen?
U dit type melding krijgen als uw app een abnormale stijging van het aantal uitzonderingen van een bepaald type gedurende een dag vertoont, vergeleken met een basislijn die is berekend over de afgelopen zeven dagen.
Machine learning-algoritmen worden gebruikt voor het detecteren van de stijging van het aantal uitzonderingen, waarbij rekening wordt gehouden met een natuurlijke groei in uw toepassingsgebruik.

## <a name="does-my-app-definitely-have-a-problem"></a>Heeft mijn app zeker een probleem?
Nee, een melding betekent niet dat uw app zeker een probleem heeft. Hoewel een buitensporig aantal uitzonderingen meestal wijst op een aanvraagprobleem, kunnen deze uitzonderingen goedaardig zijn en correct worden behandeld door uw toepassing.

## <a name="how-do-i-fix-it"></a>Hoe kan ik dit probleem oplossen?
De meldingen bevatten diagnostische informatie ter ondersteuning van het diagnoseproces:
1. **Triage.** In de melding ziet u hoeveel gebruikers of hoeveel aanvragen worden beïnvloed. Dit kan u helpen een prioriteit toe te wijzen aan het probleem.
2. **Scope.** Is het probleem van invloed op al het verkeer, of gewoon een operatie? Deze informatie kan worden verkregen uit de kennisgeving.
3. **Diagnosticeren.** De detectie bevat informatie over de methode van waaruit de uitzondering is gegooid, evenals het uitzonderingstype. U ook de gerelateerde items en rapporten gebruiken die linken naar ondersteunende informatie, om u te helpen het probleem verder te diagnosticeren.
