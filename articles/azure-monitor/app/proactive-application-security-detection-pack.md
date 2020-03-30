---
title: Beveiligingsdetectiepakket met Azure-toepassingsinzichten
description: Monitor toepassing met Azure Application Insights en Slimme detectie voor mogelijke beveiligingsproblemen.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: b23ada6fe4596a2eee242cc9145789521caf697f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669723"
---
# <a name="application-security-detection-pack-preview"></a>Detectiepakket voor toepassingsbeveiliging (voorbeeld)

Application Insights analyseert automatisch de telemetrie gegenereerd door uw toepassing en detecteert potentiële beveiligingsproblemen. Met deze mogelijkheid u potentiële beveiligingsproblemen identificeren en verwerken door de toepassing op te lossen of door de nodige beveiligingsmaatregelen te nemen.

Deze functie vereist geen speciale installatie, anders dan [het configureren van uw app om telemetrie te verzenden.](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview)

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Wanneer zou ik dit soort slimme detectiemeldingen krijgen?
Er zijn drie soorten beveiligingsproblemen die worden gedetecteerd:
1. Onveilige URL-toegang: een URL in de toepassing wordt geopend via zowel HTTP als HTTPS. Een URL die HTTPS-verzoeken accepteert, moet doorgaans geen HTTP-verzoeken accepteren. Dit kan duiden op een bug of beveiligingsprobleem in uw toepassing.
2. Onveilige vorm: een formulier (of een ander "POST"-verzoek) in de toepassing gebruikt HTTP in plaats van HTTPS. Het gebruik van HTTP kan de gebruikersgegevens die door het formulier worden verzonden, compromitteren.
3. Verdachte gebruikersactiviteit: de toepassing wordt op ongeveer hetzelfde moment vanuit meerdere landen/regio's geopend door dezelfde gebruiker. Dezelfde gebruiker heeft bijvoorbeeld binnen hetzelfde uur toegang gekregen tot de toepassing vanuit Spanje en de Verenigde Staten. Deze detectie geeft een mogelijk kwaadaardige toegang poging tot uw toepassing.

## <a name="does-my-app-definitely-have-a-security-issue"></a>Heeft mijn app zeker een beveiligingsprobleem?
Nee, een melding betekent niet dat uw app zeker een beveiligingsprobleem heeft. Een detectie van een van de bovenstaande scenario's kan in veel gevallen duiden op een beveiligingsprobleem. De detectie kan echter een natuurlijke zakelijke rechtvaardiging hebben en kan worden genegeerd.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Hoe los ik de detectie 'Onveilige URL-toegang' op?
1. **Triage.** De melding geeft het aantal gebruikers dat toegang heeft gehad tot onveilige URL's en de URL die het meest werd beïnvloed door onveilige toegang. Dit kan u helpen een prioriteit toe te wijzen aan het probleem.
2. **Scope.** Welk percentage van de gebruikers heeft toegang tot onveilige URL's? Hoeveel URL's zijn er beïnvloed? Deze informatie kan worden verkregen uit de kennisgeving.
3. **Diagnosticeren.** De detectie biedt de lijst met onveilige aanvragen en de lijsten met URL's en gebruikers die zijn getroffen, om u te helpen het probleem verder te diagnosticeren.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Hoe los ik de detectie van 'Onveilig formulier' op?
1. **Triage.** De melding bevat het aantal onveilige formulieren en het aantal gebruikers waarvan de gegevens mogelijk zijn gecompromitteerd. Dit kan u helpen een prioriteit toe te wijzen aan het probleem.
2. **Scope.** Welke vorm was betrokken bij het grootste aantal onveilige transmissies, en wat is de verdeling van onveilige transmissies in de tijd? Deze informatie kan worden verkregen uit de kennisgeving.
3. **Diagnosticeren.** De detectie biedt de lijst met onveilige formulieren en een uitsplitsing van het aantal onveilige transmissies voor elk formulier, om u te helpen het probleem verder te diagnosticeren.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Hoe los ik de detectie 'Verdachte gebruikersactiviteit' op?
1. **Triage.** De melding geeft het aantal verschillende gebruikers dat het verdachte gedrag heeft vertoond. Dit kan u helpen een prioriteit toe te wijzen aan het probleem.
2. **Scope.** Uit welke landen/regio's zijn de verdachte verzoeken afkomstig? Welke gebruiker was het meest achterdochtig? Deze informatie kan worden verkregen uit de kennisgeving.
3. **Diagnosticeren.** De detectie biedt de lijst met verdachte gebruikers en de lijst met landen/regio's voor elke gebruiker, om u te helpen het probleem verder te diagnosticeren.
