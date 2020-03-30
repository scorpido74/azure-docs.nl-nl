---
title: Een preview-omgeving diagnosticeren en oplossen - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over het diagnosticeren en oplossen van een Azure Time Series Insights Preview-omgeving.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 667dee6365f38ae058e91c61c24838d8912df26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152643"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>Een Preview-omgeving diagnosticeren en oplossen

In dit artikel worden een aantal veelvoorkomende problemen samengevat die u tegenkomen wanneer u werkt met uw Azure Time Series Insights Preview-omgeving. Het artikel beschrijft ook mogelijke oorzaken en oplossingen voor elk probleem.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Probleem: ik kan mijn omgeving niet vinden in de preview-verkenner

Dit probleem kan zich voordoen als u geen machtigingen hebt voor toegang tot de Time Series Insights-omgeving. Gebruikers hebben een toegangsrol op lezerniveau nodig om hun Time Series Insights-omgeving te bekijken. Als u de huidige toegangsniveaus wilt verifiëren en extra toegang wilt verlenen, gaat u naar de sectie **Gegevenstoegangsbeleid** op de resource Time Series Insights in de [Azure-portal.](https://portal.azure.com/)

  [![Beleid voor gegevenstoegang verifiëren.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Probleem: Er worden geen gegevens gezien in de preview-verkenner

Er zijn verschillende veelvoorkomende redenen waarom uw gegevens mogelijk niet worden weergegeven in de [Azure Time Series Insights Preview-verkenner](https://insights.timeseries.azure.com/preview).

- Uw gebeurtenisbron ontvangt mogelijk geen gegevens.

    Controleer of uw gebeurtenisbron, een gebeurtenishub of een IoT-hub, gegevens ontvangt van uw tags of instanties. Ga naar de overzichtspagina van uw bron in de Azure-portal om dit te verifiëren.

    [![Overzicht van dashboardstatistieken bekijken.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Uw gebeurtenisbrongegevens zijn niet in JSON-indeling.

    Time Series Insights ondersteunt alleen JSON-gegevens. Lees voor JSON-voorbeelden [Ondersteunde JSON-vormen](./how-to-shape-query-json.md).

- Er ontbreekt een vereiste toestemming voor uw gebeurtenisbronsleutel.

  * Voor een IoT-hub moet u de sleutel met **serviceconnect-machtiging** verstrekken.

    [![Machtigingen voor IoT-hub verifiëren.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * Zowel het beleid **iothubowner** en **service** werken omdat ze **service connect** toestemming hebben.

  * Voor een gebeurtenishub moet u de sleutel met **toestemming voor luisteren** verstrekken.
  
    [![Machtigingen voor gebeurtenishubs controleren.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * Zowel het beleid **Lezen** **als Beheren** werkt omdat ze toestemming **voor luisteren** hebben.

- Uw meegeleverde consumentengroep is niet exclusief voor Time Series Insights.

    Tijdens de registratie van een IoT-hub of gebeurtenishub geeft u de consumentengroep op die wordt gebruikt om de gegevens te lezen. Deze consumentengroep moet uniek zijn per omgeving. Als de consumentengroep wordt gedeeld, koppelt de onderliggende gebeurtenishub automatisch een van de lezers willekeurig los. Bied een unieke consumentengroep voor Time Series Insights om uit te lezen.

- De eigenschap Time Series ID die is opgegeven op het moment van inrichten is onjuist, ontbreekt of niet.

    Dit probleem kan zich voordoen als de eigenschap Time Series ID onjuist is geconfigureerd op het moment van inrichten van de omgeving. Lees [Aanbevolen procedures voor het kiezen van een time-series-id voor](./time-series-insights-update-how-to-id.md)meer informatie. Op dit moment u een bestaande Time Series Insights-omgeving niet bijwerken om een andere Time Series ID te gebruiken.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Probleem: Sommige gegevens worden weergegeven, maar sommige ontbreken

Mogelijk verzendt u gegevens zonder de Time Series ID.

- Dit probleem kan zich voordoen wanneer u gebeurtenissen verzendt zonder het veld Time Series ID in de payload. Lees [Ondersteunde JSON-shapes](./how-to-shape-query-json.md)voor meer informatie .
- Dit probleem kan zich voordoen omdat uw omgeving wordt beperkt.

    > [!NOTE]
    > Op dit moment ondersteunt Time Series Insights een maximale opnamesnelheid van 6 Mbps.

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>Probleem: Gegevens werden weergegeven, maar nu is de opname gestopt

- Uw gebeurtenisbronsleutel is mogelijk opnieuw gegenereerd en uw Preview-omgeving heeft de nieuwe gebeurtenisbronsleutel nodig.

Dit probleem treedt op wanneer de sleutel die wordt verstrekt bij het maken van uw gebeurtenisbron niet langer geldig is. U ziet telemetrie in uw hub, maar geen ingress ontvangen berichten in Time Series Insights. Als u niet zeker weet of de sleutel opnieuw is gegenereerd, u het activiteitenlogboek van uw gebeurtenishubs doorzoeken op 'Autorisatieregels voor naamruimte maken of bijwerken' of zoeken op 'IotHub-bron maken of bijwerken' voor iot-hub. 

Als u uw Time Series Insights Preview-omgeving wilt bijwerken met de nieuwe sleutel, opent u uw hubbron in de Azure-portal en kopieert u de nieuwe sleutel. Navigeer naar uw TSI-bron en klik op Gebeurtenisbronnen. 

   [![Update-toets.](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

Selecteer de gebeurtenisbron(s) waarvan de inname is gestopt, plak de nieuwe sleutel en klik op Opslaan.

   [![Update-toets.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Probleem: de naam van de eigenschap Timestamp van mijn gebeurtenisbron werkt niet

Controleer of de naam en de waarde voldoen aan de volgende regels:

* De eigenschaptijdnaam Timestamp is hoofdlettergevoelig.
* De eigenschaptijdwaarde Timestamp die afkomstig is van uw `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`gebeurtenisbron als JSON-tekenreeks heeft de indeling . Een voorbeeld van zo'n tekenreeks is `“2008-04-12T12:53Z”`.

De eenvoudigste manier om ervoor te zorgen dat de naam van uw timestamp-eigenschap wordt vastgelegd en goed werkt, is door de Time Series Insights Preview-verkenner te gebruiken. Gebruik de grafiek in de verkenner Time Series Insights Preview om een periode te selecteren nadat u de eigenschaptijd tijdstempel hebt opgegeven. Klik met de rechtermuisknop op de selectie en selecteer de optie **Gebeurtenissen verkennen.** De eerste kolomkop is de naam van de eigenschap Timestamp. Het zou `($ts)` naast het `Timestamp`woord moeten hebben, eerder dan:

* `(abc)`, wat aangeeft dat Time Series Insights de gegevenswaarden als tekenreeksen leest.
* Het **agendapictogram,** dat aangeeft dat Time Series Insights de gegevenswaarde als datumtijd leest.
* `#`, wat aangeeft dat Time Series Insights de gegevenswaarden als een geheel getal leest.

Als de eigenschap Timestamp niet expliciet is opgegeven, wordt de IoT-hub of gebeurtenishub Enqueued Time gebruikt als standaardtijdstempel.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Probleem: ik kan geen gegevens bekijken vanuit mijn warme winkel in de verkenner

- Mogelijk heeft u uw warme winkel onlangs ingericht en stromen er nog steeds gegevens binnen.
- Mogelijk hebt u uw warme winkel verwijderd, in welk geval u gegevens zou hebben verloren.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Probleem: ik kan mijn Tijdseriemodel niet bekijken of bewerken

- Mogelijk hebt u toegang tot een Time Series Insights S1- of S2-omgeving.

   Time Series-modellen worden alleen ondersteund in pay-as-you-go-omgevingen. Lees [De gegevens van Visualize in de verkenner](./time-series-insights-update-explorer.md)voor meer informatie over hoe u toegang krijgt tot uw S1- of S2-omgeving in de time series Insights Preview.

   [![Geen gebeurtenissen in de omgeving.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Mogelijk hebt u geen machtigingen om het model te bekijken en te bewerken.

   Gebruikers hebben toegang op bijdrageniveau nodig om hun Time Series-model te bewerken en te bekijken. Als u de huidige toegangsniveaus wilt verifiëren en extra toegang wilt verlenen, gaat u naar de sectie **Gegevenstoegangsbeleid** op uw Time Series Insights-bron in de Azure-portal.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Probleem: Al mijn exemplaren in de preview-verkenner hebben geen bovenliggende

Dit probleem kan optreden als er geen tijdreeksmodelhiërarchie is gedefinieerd in uw omgeving. Lees [Werken met tijdseriemodellen](./time-series-insights-update-how-to-tsm.md)voor meer informatie.

  [![Niet-bovenliggende instanties geven een waarschuwing weer.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

- Lees [Werken met tijdseriemodellen](./time-series-insights-update-how-to-tsm.md).

- Meer informatie over [ondersteunde JSON-vormen](./how-to-shape-query-json.md).

- Bekijk [planning en limieten](./time-series-insights-update-plan.md) in Azure Time Series Insights Preview.
