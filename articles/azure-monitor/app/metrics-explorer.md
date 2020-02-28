---
title: Metrische gegevens verkennen in Azure-toepassing inzichten | Microsoft Docs
description: Grafieken in metrische Explorer interpreteren en metrische Explorer-Blades aanpassen.
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: f85a8fe79e7f4f820d7c0e5b942730305e892095
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670165"
---
# <a name="exploring-metrics-in-application-insights"></a>Metrische gegevens in Application Insights verkennen
Metrische gegevens in [Application Insights][start] worden gemeten waarden en aantallen gebeurtenissen die worden verzonden via telemetrie van uw toepassing. Ze helpen prestatie problemen te detecteren en trends te bekijken in de manier waarop uw toepassing wordt gebruikt. Er is een breed scala aan standaard waarden en u kunt ook uw eigen aangepaste metrische gegevens en gebeurtenissen maken.

> [!NOTE]
> In dit artikel wordt de klassieke metrische Explorer-ervaring beschreven die momenteel is afgeschaft en zal uiteindelijk buiten gebruik worden gesteld. We raden u aan de nieuwe ervaring die in [dit artikel](../platform/metrics-charts.md)wordt beschreven, te controleren.

Metrische gegevens en aantal gebeurtenissen worden weer gegeven in grafieken van geaggregeerde waarden, zoals sommen, gemiddelden of tellingen.

Hier volgt een voor beeld van een set grafieken:

![](./media/metrics-explorer/01-overview.png)

U kunt overal in de Application Insights Portal metrische grafieken vinden. In de meeste gevallen kunnen ze worden aangepast en kunt u meer grafieken toevoegen aan de Blade. Klik op de Blade overzicht op door te gaan naar meer gedetailleerde grafieken (die functies als "servers" hebben) of klik op **Metrics Explorer** om een nieuwe Blade te openen waar u aangepaste grafieken kunt maken.

## <a name="time-range"></a>Tijdsbereik
U kunt het tijds bereik dat wordt gedekt door de grafieken of rasters op een Blade wijzigen.

![Open de Blade overzicht van uw toepassing in de Azure Portal](./media/metrics-explorer/03-range.png)

Als u bepaalde gegevens verwacht die nog niet zijn verschenen, klikt u op vernieuwen. Grafieken worden op intervallen vernieuwd, maar de intervallen zijn langer voor grotere Peri Oden. Het kan even duren voordat gegevens via de analyse pijplijn naar een grafiek worden gestuurd.

Als u wilt inzoomen op een grafiek, sleept u de muis aanwijzer over het volgende:

![Sleep over een deel van een grafiek.](./media/metrics-explorer/12-drag.png)

Klik op de knop Zoomen ongedaan maken om deze te herstellen.

## <a name="granularity-and-point-values"></a>Granulatie-en punt waarden
Beweeg de muis aanwijzer over het diagram om de waarden van de metrische gegevens op dat punt weer te geven.

![Beweeg de muis aanwijzer over een grafiek](./media/metrics-explorer/02-focus.png)

De waarde van de metriek op een bepaald punt wordt geaggregeerd over het voorafgaande steekproef interval.

Het steekproef interval of ' granulariteit ' wordt boven aan de Blade weer gegeven.

![De koptekst van een Blade.](./media/metrics-explorer/11-grain.png)

U kunt de granulariteit aanpassen op de Blade tijd bereik:

![De koptekst van een Blade.](./media/metrics-explorer/grain.png)

De granulaties die beschikbaar zijn, zijn afhankelijk van het tijds bereik dat u selecteert. De exacte granulariteit is alternatieven voor de ' automatische ' granulatie voor het tijds bereik.


## <a name="editing-charts-and-grids"></a>Grafieken en rasters bewerken
Een nieuwe grafiek toevoegen aan de Blade:

![Kies in Metrics Explorer grafiek toevoegen](./media/metrics-explorer/04-add.png)

Selecteer **bewerken** in een bestaand of nieuw diagram om te bewerken wat er wordt weer gegeven:

![Selecteer een of meer metrische gegevens](./media/metrics-explorer/08-select.png)

U kunt meer dan één metrische gegevens weer geven in een grafiek, hoewel er beperkingen zijn voor de combi naties die samen kunnen worden weer gegeven. Zodra u één metrische gegevens kiest, worden sommige van de andere uitgeschakeld.

Als u [aangepaste metrische gegevens][track] in uw app hebt gecodeerd (aanroepen naar TrackMetric en track event), worden deze hier weer gegeven.

## <a name="segment-your-data"></a>Uw gegevens segmenteren
U kunt een metriek splitsen op eigenschap, bijvoorbeeld om pagina weergaven te vergelijken op clients met verschillende besturings systemen.

Selecteer een grafiek of raster, schakel over op groeperen en kies een eigenschap om te groeperen op:

![Selecteer groeperen op en stel vervolgens een eigenschap in Group by in op](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> Wanneer u groepering gebruikt, bieden de vlak-en staaf grafiek typen een gestapelde weer gave. Dit is geschikt wanneer de aggregatie methode sum is. Maar wanneer het aggregatie type gemiddelde is, kiest u de weergave typen lijn of raster.
>
>

Als u [aangepaste metrische gegevens][track] in uw app hebt gecodeerd en deze eigenschaps waarden bevatten, kunt u de eigenschap in de lijst selecteren.

Is de grafiek te klein voor gesegmenteerde gegevens? De hoogte aanpassen:

![De schuif regelaar aanpassen](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Aggregatie typen
De legenda aan de zijkant bevat normaal gesp roken standaard de cumulatieve waarde over de periode van de grafiek. Als u de muis aanwijzer boven de grafiek houdt, wordt de waarde op dat punt weer gegeven.

Elk gegevens punt in de grafiek is een aggregatie van de gegevens waarden die in het voor gaande steekproef interval of ' granulatie ' zijn ontvangen. De granulariteit wordt boven aan de Blade weer gegeven en is afhankelijk van de totale tijd schaal van de grafiek.

Metrische gegevens kunnen op verschillende manieren worden geaggregeerd:

* **Count** is een telling van de gebeurtenissen die worden ontvangen in het steekproef interval. Dit wordt gebruikt voor gebeurtenissen zoals aanvragen. Variaties in de hoogte van de grafiek duiden op de snelheid waarmee de gebeurtenissen plaatsvinden. Maar houd er rekening mee dat de numerieke waarde verandert wanneer u het steekproef interval wijzigt.
* Met **Sum** worden de waarden van alle gegevens punten die zijn ontvangen over het steekproef interval of de periode van de grafiek opgeteld.
* Met **gemiddelde** wordt de som verdeeld op basis van het aantal gegevens punten dat over het interval wordt ontvangen.
* **Unieke** aantallen worden gebruikt voor het aantal gebruikers en accounts. Over het steekproef interval, of over de periode van de grafiek, toont de afbeelding het aantal verschillende gebruikers dat in die tijd wordt weer gegeven.
* **%** -percentage versies van elke aggregatie worden alleen gebruikt voor gesegmenteerde grafieken. Het totaal voegt altijd Maxi maal 100% toe en de grafiek toont de relatieve bijdrage van verschillende onderdelen van een totaal.

    ![Percentage aggregatie](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Het aggregatie type wijzigen

![Bewerk het diagram en selecteer vervolgens aggregatie](./media/metrics-explorer/05-aggregation.png)

De standaard methode voor elke metriek wordt weer gegeven wanneer u een nieuwe grafiek maakt of wanneer alle metrische gegevens zijn opheffen:

![De selectie van alle metrische gegevens opheffen om de standaard waarden weer te geven](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Y-as vastmaken 
Standaard toont een grafiek waarden van de Y-as, te beginnen met nul tot maximum waarden in het gegevens bereik, om een visuele representatie van Quantum van de waarden te geven. Maar in sommige gevallen kan het nuttig zijn om kleine wijzigingen in waarden visueel te controleren. Voor aanpassingen zoals dit gebruikt u de functie voor het bewerken van het bereik van de Y-as om de minimum-of maximum waarde van de Y-as vast te maken op de gewenste plaats.
Klik op geavanceerde instellingen selectie vakje om de instellingen voor het bereik van de Y-as weer te geven

![Klik op geavanceerde instellingen, selecteer aangepast bereik en geef minimale maximum waarden op](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Uw gegevens filteren
Om alleen de metrische gegevens voor een geselecteerde set eigenschaps waarden weer te geven:

![Klik op filter, vouw een eigenschap uit en controleer enkele waarden](./media/metrics-explorer/19-filter.png)

Als u geen waarden voor een bepaalde eigenschap selecteert, is het hetzelfde als het selecteren van deze allemaal: er is geen filter voor die eigenschap.

Let op het aantal gebeurtenissen naast elke eigenschaps waarde. Wanneer u waarden van één eigenschap selecteert, worden de aantallen naast andere eigenschaps waarden aangepast.

Filters zijn van toepassing op alle grafieken op een Blade. Als u verschillende filters wilt Toep assen op verschillende grafieken, maakt u verschillende Blades met metrische gegevens en slaat u deze op. Als u wilt, kunt u grafieken van verschillende Blades vastmaken aan het dash board, zodat u ze naast elkaar kunt zien.

### <a name="remove-bot-and-web-test-traffic"></a>Bot en web-test verkeer verwijderen
Gebruik het filter **werkelijk of synthetisch verkeer** en controleer **real**.

U kunt ook filteren op **bron van synthetisch verkeer**.

### <a name="to-add-properties-to-the-filter-list"></a>Eigenschappen toevoegen aan de filter lijst
Wilt u telemetrie filteren op een categorie van uw eigen keuze? U kunt bijvoorbeeld uw gebruikers verdelen over verschillende categorieën en u wilt uw gegevens segmenteren op basis van deze categorieën.

[Maak uw eigen eigenschap](../../azure-monitor/app/api-custom-events-metrics.md#properties). Stel deze in een [telemetrie-initialisatie functie](../../azure-monitor/app/api-custom-events-metrics.md#defaults) in zodat deze wordt weer gegeven in alle telemetrie, inclusief de standaard-telemetrie die wordt verzonden door verschillende SDK-modules.

## <a name="edit-the-chart-type"></a>Het grafiek type bewerken
U ziet dat u kunt scha kelen tussen rasters en grafieken:

![Selecteer een raster of grafiek en kies vervolgens een grafiek type](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>De Blade metrische gegevens opslaan
Wanneer u een aantal grafieken hebt gemaakt, slaat u ze op als favoriet. U kunt kiezen of u deze wilt delen met andere team leden, als u een organisatie-account gebruikt.

![Kies een favoriet](./media/metrics-explorer/21-favorite-save.png)

Als u de Blade opnieuw wilt weer geven, **gaat u naar de Blade overzicht** en opent u favorieten:

![Kies op de Blade overzicht de optie Favorieten](./media/metrics-explorer/22-favorite-get.png)

Als u tijdens het opslaan een relatief tijds bereik hebt gekozen, wordt de Blade bijgewerkt met de meest recente metrische gegevens. Als u een absoluut tijds bereik hebt gekozen, worden dezelfde gegevens elke keer weer gegeven.

## <a name="reset-the-blade"></a>De Blade opnieuw instellen
Als u een Blade bewerkt, maar u wilt teruggaan naar de oorspronkelijke opgeslagen set, klikt u op opnieuw instellen.

![In de knoppen boven aan metrische Explorer](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Live metrische gegevens stroom

Open [Live Stream](live-stream.md)voor een veel meer directe weer gave van uw telemetrie. De meeste metrische gegevens duren een paar minuten om te worden weer gegeven, vanwege het aggregatie proces. Dynamische metrische gegevens worden daarentegen geoptimaliseerd voor een lage latentie. 

## <a name="set-alerts"></a>Waarschuwingen instellen
Voeg een waarschuwing toe om een melding te ontvangen van een e-mail bericht van ongebruikelijke waarden van een wille keurige waarde. U kunt kiezen of u het e-mail bericht wilt verzenden naar de account beheerders of naar specifieke e-mail adressen.

![Kies in Metrics Explorer waarschuwings regels, waarschuwing toevoegen](./media/metrics-explorer/appinsights-413setMetricAlert.png)

Meer [informatie over waarschuwingen][alerts].


## <a name="continuous-export"></a>Continue export
Als u gegevens continu wilt exporteren, zodat u deze extern kunt verwerken, kunt u overwegen [continue export](../../azure-monitor/app/export-telemetry.md)te gebruiken.

### <a name="power-bi"></a>Power BI
Als u zelfs rijkere weer gaven van uw gegevens wilt, kunt u [exporteren naar Power bi](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Analyse
[Analytics](../../azure-monitor/app/analytics.md) is een veelzijdigere manier om uw telemetrie te analyseren met behulp van een krachtige query taal. Gebruik deze functie als u resultaten wilt combi neren of berekenen op basis van metrische gegevens, of als u een uitgebreid onderzoek wilt doen naar de recente prestaties van uw app. 

U kunt vanuit een metrische grafiek op het analyse pictogram klikken om rechtstreeks naar de overeenkomende analyse query te gaan.

## <a name="troubleshooting"></a>Problemen oplossen
*Ik zie geen gegevens in mijn grafiek.*

* Filters zijn van toepassing op alle grafieken op de Blade. Zorg ervoor dat, terwijl u zich in de ene grafiek bevindt, geen filter is ingesteld waarmee alle gegevens op een andere worden uitgesloten.

    Als u verschillende filters op verschillende grafieken wilt instellen, maakt u deze op verschillende Blades en slaat u ze op als afzonderlijke favorieten. Als u wilt, kunt u deze vastmaken aan het dash board, zodat u ze naast elkaar kunt zien.
* Als u een grafiek groepeert op basis van een eigenschap die niet op de metriek is gedefinieerd, is er niets in de grafiek. Probeer Group by te wissen of kies een andere groeperings eigenschap.
* Prestatie gegevens (CPU, i/o-snelheid, enzovoort) zijn beschikbaar voor Java-webservices, Windows-bureau blad-apps, [IIS-Web-apps en-services als u status monitor](../../azure-monitor/app/monitor-performance-live-website-now.md)en [Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md)installeert. Het is niet beschikbaar voor Azure websites.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Volgende stappen
* [Gebruik controleren met Application Insights](../../azure-monitor/app/usage-overview.md)
* [Diagnostische Zoek opdrachten gebruiken](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
