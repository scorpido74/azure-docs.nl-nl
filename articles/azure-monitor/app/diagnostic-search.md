---
title: Zoeken gebruiken in Azure Application Insights | Microsoft Documenten
description: Zoek en filter ruwe telemetrie die door uw web-app wordt verzonden.
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: 8039a55784f63030f330d6c1e2061e99b8b63bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275982"
---
# <a name="using-search-in-application-insights"></a>Zoeken gebruiken in toepassingsinzichten

Zoeken is een functie van [Application Insights](../../azure-monitor/app/app-insights-overview.md) die u gebruikt om afzonderlijke telemetrie-items te vinden en te verkennen, zoals paginaweergaven, uitzonderingen of webverzoeken. En u logboeksporen en gebeurtenissen bekijken die u hebt gecodeerd.

(Voor complexere query's over uw gegevens u [Analytics](../../azure-monitor/log-query/get-started-portal.md)gebruiken .)

## <a name="where-do-you-see-search"></a>Waar zie je Zoeken?

### <a name="in-the-azure-portal"></a>In de Azure Portal

U diagnostische zoekopdrachten openen via het tabblad Overzicht van toepassingstatistieken van uw toepassing (in de bovenste balk) of onder onderzoek aan de linkerkant.

![Tabblad Zoeken](./media/diagnostic-search/view-custom-events.png)

Ga naar het vervolgkeuzemenu van gebeurtenistypen om een lijst met telemetrie-items te bekijken: serveraanvragen, paginaweergaven, aangepaste gebeurtenissen die u hebt gecodeerd, enzovoort. Bovenaan de lijst met resultaten staat een overzichtsdiagram met het aantal gebeurtenissen in de loop van de tijd.

Klik uit het vervolgkeuzemenu of Vernieuw om nieuwe gebeurtenissen te krijgen.

### <a name="in-visual-studio"></a>In Visual Studio

In Visual Studio is er ook een toepassingsinzichtenzoekvenster. Het is vooral handig voor het weergeven van telemetriegebeurtenissen die worden gegenereerd door de toepassing die u debugadt. Maar het kan ook de gebeurtenissen weergeven die zijn verzameld vanuit uw gepubliceerde app op de Azure-portal.

Open het zoekvenster in Visual Studio:

![Visual Studio opent zoeken naar Application Insights](./media/diagnostic-search/32.png)

Het zoekvenster heeft functies die vergelijkbaar zijn met de webportal:

![Zoekvenster Visual Studio Application Insights](./media/diagnostic-search/34.png)

Het tabblad Bewerking bijhouden is beschikbaar wanneer u een aanvraag of een paginaweergave opent. Een 'bewerking' is een reeks gebeurtenissen waaraan een enkele aanvraag- of paginaweergave is gekoppeld. Afhankelijkheidsoproepen, uitzonderingen, traceerlogboeken en aangepaste gebeurtenissen kunnen bijvoorbeeld deel uitmaken van één bewerking. Op het tabblad Bewerking bijhouden worden de timing en duur van deze gebeurtenissen met betrekking tot de aanvraag- of paginaweergave grafisch weergegeven.

## <a name="inspect-individual-items"></a>Afzonderlijke artikelen inspecteren

Selecteer een telemetrie-item om belangrijke velden en gerelateerde items te bekijken.

![Schermafbeelding van een individuele afhankelijkheidsaanvraag](./media/diagnostic-search/telemetry-item.png)

Hiermee wordt de end-to-end transactiedetailsweergave gestart.

## <a name="filter-event-types"></a>Gebeurtenistypen filteren

Open het vervolgkeuzemenu van de gebeurtenistypen en kies de gebeurtenistypen die u wilt zien. (Als u later de filters wilt herstellen, klikt u op Opnieuw instellen.)

De soorten evenementen zijn:

* **Traceer** - [diagnostische logboeken,](../../azure-monitor/app/asp-net-trace-logs.md) waaronder TrackTrace, log4Net, NLog en System.Diagnostic.Trace calls.
* **Aanvraag** - HTTP-aanvragen die door uw servertoepassing zijn ontvangen, inclusief pagina's, scripts, afbeeldingen, stijlbestanden en gegevens. Deze gebeurtenissen worden gebruikt om de overzichtsdiagrammen voor aanvragen en antwoorden te maken.
* **Paginaweergave** - [Telemetrie verzonden door de webclient](../../azure-monitor/app/javascript.md), gebruikt om paginaweergaverapporten te maken.
* **Aangepaste gebeurtenis** - Als u oproepen naar TrackEvent() hebt ingevoegd om het gebruik te [controleren,](../../azure-monitor/app/api-custom-events-metrics.md)u deze hier doorzoeken.
* **Uitzondering** - Niet-gevangen [uitzonderingen in de server](../../azure-monitor/app/asp-net-exceptions.md)en de uitzonderingen die u registreert met Behulp van TrackException().
* **Afhankelijkheidsoproepen** - [van uw servertoepassing](../../azure-monitor/app/asp-net-dependencies.md) naar andere services zoals REST API's of databases en AJAX-oproepen vanuit uw [clientcode.](../../azure-monitor/app/javascript.md)
* **Beschikbaarheid** - Resultaten van [beschikbaarheidstests](../../azure-monitor/app/monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filteren op eigenschapswaarden

U gebeurtenissen filteren op de waarden van hun eigenschappen. De beschikbare eigenschappen zijn afhankelijk van de geselecteerde gebeurtenistypen. Klik op het filterpictogram ![Pictogram Filter](./media/diagnostic-search/filter-icon.png) om te beginnen.

Het kiezen van geen waarden van een bepaalde eigenschap heeft hetzelfde effect als het kiezen van alle waarden. Het schakelt filteren op die eigenschap uit.

De tellingen rechts van de filterwaarden geven aan hoeveel exemplaren er in de huidige gefilterde set staan.

## <a name="find-events-with-the-same-property"></a>Gebeurtenissen zoeken met dezelfde eigenschap

Als u alle items met dezelfde eigenschapswaarde wilt zoeken, typt u deze in de zoekbalk of klikt u op het selectievakje wanneer u door eigenschappen op het filtertabblad kijkt.

![Klik op het selectievakje van een eigenschap op het filtertabblad](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>De gegevens doorzoeken

> [!NOTE]
> Als u complexere query's wilt schrijven, opent u [**Logboeken (Analytics)**](../../azure-monitor/log-query/get-started-portal.md) vanaf de bovenkant van het hoofdzoeken.
>

U zoeken naar termen in een van de waarde van de eigenschap. Dit is handig als u [aangepaste gebeurtenissen](../../azure-monitor/app/api-custom-events-metrics.md) met eigenschapswaarden hebt geschreven.

U een tijdsbereik instellen, omdat zoekopdrachten over een korter bereik sneller zijn.

![Diagnostische zoekopdracht openen](./media/diagnostic-search/search-property.png)

Zoek naar volledige woorden, niet naar substrings. Gebruik aanhalingstekens om speciale tekens bij te sluiten.

| Tekenreeks | *Niet* gevonden | Gevonden |
| --- | --- | --- |
| HomeController.Over |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|Verenigde Staten|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

Dit zijn de zoekexpressies die u gebruiken:

| Voorbeeldquery | Effect |
| --- | --- |
| `apple` |Zoek alle gebeurtenissen in het tijdsbereik waarvan de velden het woord "appel" bevatten |
| `apple AND banana` <br/>`apple banana` |Zoek gebeurtenissen die beide woorden bevatten. Gebruik kapitaal "AND", niet "en". <br/>Korte vorm. |
| `apple OR banana` |Zoek gebeurtenissen die een van beide woorden bevatten. Gebruik "OR", niet "of". |
| `apple NOT banana` |Zoek gebeurtenissen die het ene woord bevatten, maar niet het andere. |

## <a name="sampling"></a>Steekproeven

Als uw app een grote hoeveelheid telemetrie genereert (en u de ASP.NET SDK-versie 2.0.0-beta3 of hoger gebruikt), vermindert de adaptieve bemonsteringsmodule automatisch het volume dat naar de portal wordt verzonden door slechts een representatieve fractie van gebeurtenissen te verzenden. Gebeurtenissen die gerelateerd zijn aan dezelfde aanvraag, worden echter geselecteerd of uitgeschakeld als een groep, zodat u navigeren tussen gerelateerde gebeurtenissen.

[Meer informatie over steekproeven](../../azure-monitor/app/sampling.md).

## <a name="create-work-item"></a>Werkitem maken

U een bug maken in GitHub of Azure DevOps met de details van elk telemetrie-item.

Ga naar de end-to-end transactiedetailweergave door op een telemetrieitem te klikken en vervolgens **werkitem maken te**selecteren .

![Klik op Nieuw werkitem, bewerk de velden en klik vervolgens op OK.](./media/diagnostic-search/work-item.png)

De eerste keer dat u dit doet, wordt u gevraagd om een koppeling naar uw Azure DevOps-organisatie en -project te configureren.

(U de koppeling ook configureren op het tabblad Werkitems.)

## <a name="send-more-telemetry-to-application-insights"></a>Meer telemetrie verzenden naar Application Insights

Naast de kant-en-klare telemetrie die door Application Insights SDK wordt verzonden, u:

* Leg logboeksporen vast van uw favoriete logging framework in [.NET](../../azure-monitor/app/asp-net-trace-logs.md) of [Java](../../azure-monitor/app/java-trace-logs.md). Dit betekent dat u uw logboeksporen doorzoeken en deze correleren met paginaweergaven, uitzonderingen en andere gebeurtenissen.
* [Schrijf code](../../azure-monitor/app/api-custom-events-metrics.md) om aangepaste gebeurtenissen, paginaweergaven en uitzonderingen te verzenden.

[Meer informatie over het verzenden van logboeken en aangepaste telemetrie naar Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).

## <a name="q--a"></a><a name="questions"></a>Q & A

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Hoeveel gegevens worden bewaard?

Zie het [overzicht Limieten](../../azure-monitor/app/pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Hoe kan ik POST-gegevens zien in mijn serveraanvragen?

We loggen de POST-gegevens niet automatisch in, maar u [TrackTrace gebruiken of oproepen loggen.](../../azure-monitor/app/asp-net-trace-logs.md) Plaats de POST-gegevens in de berichtparameter. U het bericht niet op dezelfde manier filteren als u op eigenschappen filteren, maar de groottelimiet is langer.

## <a name="next-steps"></a><a name="add"></a>Volgende stappen

* [Complexe query's schrijven in Analytics](../../azure-monitor/log-query/get-started-portal.md)
* [Logboeken en aangepaste telemetrie verzenden naar Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
* [Beschikbaarheids- en responsiviteitstests instellen](../../azure-monitor/app/monitor-web-app-availability.md)
* [Probleemoplossing](../../azure-monitor/app/troubleshoot-faq.md)
