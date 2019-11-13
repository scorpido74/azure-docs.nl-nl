---
title: Een voorbeeld omgeving diagnosticeren en problemen oplossen-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het vaststellen en oplossen van problemen met een Azure Time Series Insights-voorbeeld omgeving.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/22/2019
ms.custom: seodec18
ms.openlocfilehash: df8300e84309a874faa4b1c06891a4c5b549fce6
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014784"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>Een voorbeeld omgeving diagnosticeren en problemen oplossen

In dit artikel vindt u een overzicht van enkele veelvoorkomende problemen die u kunt tegen komen wanneer u met uw Azure Time Series Insights-voorbeeld omgeving werkt. In het artikel worden ook mogelijke oorzaken en oplossingen voor elk probleem beschreven.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Probleem: Ik kan mijn omgeving niet vinden in de preview-versie van Verkenner

Dit probleem kan zich voordoen als u geen machtigingen hebt voor toegang tot de Time Series Insights omgeving. Gebruikers hebben een toegangs functie op lezerniveau nodig om hun Time Series Insights omgeving weer te geven. Als u de huidige toegangs niveaus wilt controleren en aanvullende toegang wilt verlenen, gaat u naar de sectie **Data Access policies** op de time series Insights-bron in de [Azure Portal](https://portal.azure.com/).

  [![omgeving](media/v2-update-diagnose-and-troubleshoot/environment.png)](media/v2-update-diagnose-and-troubleshoot/environment.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Probleem: er worden geen gegevens weer gegeven in de preview Explorer

Er zijn verschillende veelvoorkomende redenen waarom u uw gegevens mogelijk niet ziet in de [Azure time series Insights preview Explorer](https://insights.timeseries.azure.com/preview).

- De bron van de gebeurtenis kan geen gegevens ontvangen.

    Controleer of uw gebeurtenis bron, een Event Hub of een IoT-hub, gegevens ontvangt van uw tags of exemplaren. Als u wilt controleren, gaat u naar de overzichts pagina van uw resource in de Azure Portal.

    [![Dashboard-insights](media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png)](media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png#lightbox)

- De bron gegevens van uw gebeurtenis bevindt zich niet in JSON-indeling.

    Time Series Insights ondersteunt alleen JSON-gegevens. Zie voor voorbeelden van JSON, [ondersteund JSON-vormen](./how-to-shape-query-json.md).

- Er ontbreekt een vereiste machtiging voor de bron sleutel van uw gebeurtenis.

  * Voor een IoT-hub moet u de sleutel opgeven die **service Connect** -machtiging heeft.

    [![Configuratie](media/v2-update-diagnose-and-troubleshoot/configuration.png)](media/v2-update-diagnose-and-troubleshoot/configuration.png#lightbox)

  * Zoals in de voor gaande afbeelding wordt weer gegeven, werken zowel het beleid **iothubowner** als het **service** werk omdat ze de machtiging **service CONNECT** hebben.
  * Voor een Event Hub moet u de sleutel met de machtiging **Luis teren** opgeven.
  
    [![machtigingen](media/v2-update-diagnose-and-troubleshoot/permissions.png)](media/v2-update-diagnose-and-troubleshoot/permissions.png#lightbox)

  * Zoals in de voor gaande afbeelding wordt weer gegeven, werken beide beleids regels voor **lezen** en **beheren** omdat ze de machtiging **Luis teren** hebben.

- Uw verleende consumenten groep is niet exclusief voor de Time Series Insights.

    Tijdens de registratie van een IoT hub of Event Hub geeft u de Consumer groep op die wordt gebruikt om de gegevens te lezen. Deze Consumer groep moet uniek zijn per omgeving. Als de Consumer groep wordt gedeeld, wordt de verbinding met een van de lezers op wille keurige wijze automatisch verbroken door de onderliggende Event Hub. Geef een unieke consumenten groep op voor de Time Series Insights om te lezen.

- De eigenschap voor de tijd reeks-ID die is opgegeven bij het inrichten, is onjuist, ontbreekt of is null.

    Dit probleem kan optreden als de time series-ID-eigenschap onjuist is geconfigureerd op het moment van de inrichting van de omgeving. Zie [Aanbevolen procedures voor het kiezen van een time series-id](./time-series-insights-update-how-to-id.md)voor meer informatie. Op dit moment kunt u een bestaande Time Series Insights omgeving niet bijwerken om een andere tijd reeks-ID te gebruiken.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Probleem: sommige gegevens worden weer gegeven, maar er ontbreekt een deel

Mogelijk verzendt u gegevens zonder de tijd reeks-ID.

- Dit probleem kan optreden wanneer u gebeurtenissen verzendt zonder het veld tijd reeks-ID in de payload. Zie [ondersteunde JSON-vormen](./how-to-shape-query-json.md)voor meer informatie.
- Dit probleem kan optreden omdat uw omgeving wordt beperkt.

    > [!NOTE]
    > Op dit moment ondersteunt Time Series Insights een maximum opname snelheid van 6 Mbps.

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Probleem: de eigenschaps naam van de tijds tempel van de gebeurtenis bron werkt niet

Zorg ervoor dat de naam en waarde aan de volgende regels voldoet:

* De naam van de tijds tempel eigenschap is hoofdletter gevoelig.
* De waarde van de tijds tempel eigenschap die afkomstig is van uw gebeurtenis bron als JSON-teken reeks heeft de indeling `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Een voorbeeld van een tekenreeks is die is `“2008-04-12T12:53Z”`.

De eenvoudigste manier om ervoor te zorgen dat de naam van de tijds tempel eigenschap wordt vastgelegd en correct werkt, is door de Time Series Insights preview Explorer te gebruiken. Gebruik in de Time Series Insights preview Explorer de grafiek om een periode te selecteren nadat u de naam van de tijds tempel eigenschap hebt ingesteld. Klik met de rechter muisknop op de selectie en selecteer de optie **gebeurtenissen verkennen** . De eerste kolomkop is de naam van de eigenschap time stamp. `($ts)` moet naast het woord `Timestamp`staan, in plaats van:

* `(abc)`, wat aangeeft dat Time Series Insights de gegevens waarden als teken reeksen leest.
* Het **kalender** pictogram, waarmee wordt aangegeven dat Time Series Insights de gegevens waarde als datum/tijd leest.
* `#`, wat aangeeft dat Time Series Insights de gegevens waarden als een geheel getal leest.

Als de tijds tempel eigenschap niet expliciet is opgegeven, wordt de IoT-hub van een gebeurtenis of Event Hub tijd in de wachtrij gebruikt als de standaard tijds tempel.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Probleem: Ik kan geen gegevens uit mijn warme Store weer geven in de Explorer

- Mogelijk hebt u onlangs uw warme Store ingericht en blijven de gegevens in de stroom.
- Mogelijk hebt u uw warme archief verwijderd. in dat geval hebt u gegevens verloren.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Probleem: Ik kan mijn time series-model niet weer geven of bewerken

- U hebt mogelijk toegang tot een Time Series Insights S1-of S2-omgeving.

   Time Series-modellen worden alleen ondersteund in omgevingen met betalen per gebruik. Zie [gegevens visualiseren in de Explorer](./time-series-insights-update-explorer.md)voor meer informatie over het openen van de S1-of S2-omgeving vanuit de time series Insights preview Explorer.

   [![Access](media/v2-update-diagnose-and-troubleshoot/access.png)](media/v2-update-diagnose-and-troubleshoot/access.png#lightbox)

- Mogelijk bent u niet gemachtigd om het model weer te geven en te bewerken.

   Gebruikers hebben toegang op Inzender niveau nodig om hun time series-model te bewerken en weer te geven. Als u de huidige toegangs niveaus wilt controleren en aanvullende toegang wilt verlenen, gaat u naar de sectie **Data Access policies** op uw time series Insights-bron in de Azure Portal.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Probleem: al mijn instanties in de preview Explorer hebben geen bovenliggend item

Dit probleem kan zich voordoen als in uw omgeving geen hiërarchie voor tijdreeks modellen is gedefinieerd. Zie [werken met Time Series-modellen](./time-series-insights-update-how-to-tsm.md)voor meer informatie.

  [![Time Series-modellen](media/v2-update-diagnose-and-troubleshoot/tsm.png)](media/v2-update-diagnose-and-troubleshoot/tsm.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

- Lees de informatie [over het werken met Time Series-modellen](./time-series-insights-update-how-to-tsm.md).
- Meer informatie over [ondersteunde JSON-vormen](./how-to-shape-query-json.md).
- Bekijk [planning en limieten](./time-series-insights-update-plan.md) in azure time series Insights preview.
