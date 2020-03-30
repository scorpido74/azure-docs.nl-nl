---
title: Degradatie in de verhouding traceringsernst - Azure Application Insights
description: Monitor toepassingssporen met Azure Application Insights voor ongebruikelijke patronen in trace telemetrie met Slimme detectie.
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 30bdd30ac9c49bb79a3c48bae8149ec761756dd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671678"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Degradatie in traceringsverhouding (voorbeeld)

Sporen worden veel gebruikt in toepassingen, omdat ze helpen het verhaal te vertellen van wat er achter de schermen gebeurt. Wanneer er iets misgaat, bieden sporen cruciaal inzicht in de volgorde van gebeurtenissen die leiden tot de ongewenste toestand. Terwijl sporen zijn over het algemeen ongestructureerd, er is een ding dat concreet kan worden geleerd van hen - hun ernst niveau. In de steady state van een toepassing, zouden we verwachten dat de verhouding tussen "goede" sporen (*Info* en *Verbose*) en "slechte" sporen (*Waarschuwing*, *Fout*en *Kritisch*) stabiel te blijven. De veronderstelling is dat "slechte" sporen kunnen gebeuren op een regelmatige basis tot op zekere hoogte te wijten aan een aantal redenen (tijdelijke netwerk problemen bijvoorbeeld). Maar wanneer een echt probleem begint te groeien, manifesteert het zich meestal als een toename van het relatieve aandeel van "slechte" sporen vs "goede" sporen. Application Insights Smart Detection analyseert automatisch de sporen die door uw toepassing zijn vastgelegd en kan u waarschuwen voor ongebruikelijke patronen in de ernst van uw trace telemetrie.

Deze functie vereist geen speciale installatie, anders dan het configureren van trace logging voor uw app (zie hoe u een trace log listener configureren voor [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) of [Java).](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs) Het is actief wanneer uw app voldoende uitzonderingtelemetrie genereert.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Wanneer zou ik dit soort slimme detectiemeldingen krijgen?
U dit type melding krijgen als de verhouding tussen goede sporen (traces die zijn vastgelegd met een niveau *van Info* of *Verbose)* en slechte sporen (traces die zijn vastgelegd met een niveau van *waarschuwing,* *fout*of *fatale)* op een bepaalde dag vernederend is, vergeleken met een basislijn berekend over de voorgaande zeven dagen.

## <a name="does-my-app-definitely-have-a-problem"></a>Heeft mijn app zeker een probleem?
Nee, een melding betekent niet dat uw app zeker een probleem heeft. Hoewel een verslechtering van de verhouding tussen "goede" en "slechte" sporen kan duiden op een toepassingsprobleem, kan deze verandering in verhouding goedaardig zijn. De toename kan bijvoorbeeld te wijten zijn aan een nieuwe stroom in de toepassing die meer "slechte" sporen uitzendt dan bestaande stromen).

## <a name="how-do-i-fix-it"></a>Hoe kan ik dit probleem oplossen?
De meldingen bevatten diagnostische informatie ter ondersteuning van het diagnoseproces:
1. **Triage.** In de melding ziet u hoeveel bewerkingen worden beïnvloed. Dit kan u helpen een prioriteit toe te wijzen aan het probleem.
2. **Scope.** Is het probleem van invloed op al het verkeer, of gewoon een operatie? Deze informatie kan worden verkregen uit de kennisgeving.
3. **Diagnosticeren.** U de gerelateerde items en rapporten gebruiken die linken naar ondersteunende informatie, om u te helpen het probleem verder te diagnosticeren.


