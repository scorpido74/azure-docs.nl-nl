---
title: Statistieken verkennen in Azure Application Insights | Microsoft Documenten
description: Grafieken interpreteren op metrische explorer en hoe u metrische explorerbladen aanpassen.
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: f85a8fe79e7f4f820d7c0e5b942730305e892095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275891"
---
# <a name="exploring-metrics-in-application-insights"></a>Statistieken verkennen in applicatieinzichten
Statistieken in [Application Insights][start] zijn gemeten waarden en tellingen van gebeurtenissen die vanuit uw toepassing telemetrie worden verzonden. Ze helpen u prestatieproblemen op te sporen en trends in de manier waarop uw toepassing wordt gebruikt, te bekijken. Er is een breed scala aan standaardstatistieken en je ook je eigen aangepaste statistieken en gebeurtenissen maken.

> [!NOTE]
> In dit artikel wordt de ervaring van de klassieke metrics explorer beschreven die momenteel wordt afgeschaft en uiteindelijk met pensioen zal gaan. We raden u aan de nieuwe ervaring die wordt beschreven in [dit artikel](../platform/metrics-charts.md).

Statistieken en gebeurtenistellingen worden weergegeven in grafieken met geaggregeerde waarden, zoals sommen, gemiddelden of tellingen.

Hier is een voorbeeld set van grafieken:

![](./media/metrics-explorer/01-overview.png)

U vindt overal metrics charts in de Application Insights portal. In de meeste gevallen kunnen ze worden aangepast en u meer grafieken aan het blad toevoegen. Klik in het blad Overzicht door naar meer gedetailleerde grafieken (met titels zoals 'Servers'), of klik op **Metrics Explorer** om een nieuw blad te openen waar u aangepaste grafieken maken.

## <a name="time-range"></a>Tijdsbereik
U het tijdsbereik wijzigen dat wordt gedekt door de grafieken of rasters op elk blad.

![Het overzichtsblad van uw toepassing openen in de Azure-portal](./media/metrics-explorer/03-range.png)

Als u gegevens verwacht die nog niet zijn verschenen, klikt u op Vernieuwen. Grafieken vernieuwen zichzelf met tussenpozen, maar de intervallen zijn langer voor grotere tijdsbereiken. Het kan even duren voordat gegevens via de analysepijplijn op een grafiek komen.

Als u wilt inzoomen op een deel van een grafiek, sleept u er overheen:

![Sleep over een deel van een grafiek.](./media/metrics-explorer/12-drag.png)

Klik op de knop Zoom ongedaan maken om deze te herstellen.

## <a name="granularity-and-point-values"></a>Granulariteit en puntwaarden
Plaats de muisaanwijzer op de grafiek om de waarden van de statistieken op dat moment weer te geven.

![De muis boven een grafiek zweven](./media/metrics-explorer/02-focus.png)

De waarde van de statistiek op een bepaald punt wordt geaggregeerd over het voorgaande bemonsteringsinterval.

Het bemonsteringsinterval of de "granulariteit" wordt boven aan het blad weergegeven.

![De kop van een mes.](./media/metrics-explorer/11-grain.png)

U de granulariteit in het tijdsbereikblad aanpassen:

![De kop van een mes.](./media/metrics-explorer/grain.png)

De beschikbare granulariteiten zijn afhankelijk van het tijdsbereik dat u selecteert. De expliciete granulariteiten zijn alternatieven voor de "automatische" granulariteit voor het tijdsbereik.


## <a name="editing-charts-and-grids"></a>Grafieken en rasters bewerken
Ga als lid van het blad naar een nieuwe grafiek:

![Kies Grafiek toevoegen in Metrics Explorer](./media/metrics-explorer/04-add.png)

Selecteer **Bewerken** in een bestaande of nieuwe grafiek om te bewerken wat het laat zien:

![Selecteer een of meer statistieken](./media/metrics-explorer/08-select.png)

U meer dan één statistiek op een grafiek weergeven, maar er zijn beperkingen voor de combinaties die samen kunnen worden weergegeven. Zodra u één statistiek kiest, zijn sommige van de andere gegevens uitgeschakeld.

Als u [aangepaste statistieken][track] in uw app hebt gecodeerd (oproepen naar TrackMetric en TrackEvent), worden deze hier weergegeven.

## <a name="segment-your-data"></a>Uw gegevens segmenteren
U een statistiek splitsen op eigenschap, bijvoorbeeld om paginaweergaven van clients met verschillende besturingssystemen te vergelijken.

Selecteer een grafiek of raster, schakel groepering in en kies een eigenschap die u wilt groeperen op:

![Selecteer Groeperen op en stel vervolgens een eigenschap selecteren in Groep Op](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> Wanneer u groepering gebruikt, bieden de typen gebieds- en staafdiagrameen gestapeld scherm. Dit is geschikt wanneer de aggregatiemethode Som is. Maar wanneer het aggregatietype Gemiddeld is, kiest u de weergavetypen Lijn of Raster.
>
>

Als u [aangepaste statistieken][track] in uw app hebt gecodeerd en eigenschapswaarden bevat, u de eigenschap in de lijst selecteren.

Is de grafiek te klein voor gesegmenteerde gegevens? De hoogte aanpassen:

![De schuifregelaar aanpassen](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Aggregatietypen
De legenda aan de zijkant geeft standaard de geaggregeerde waarde over de periode van de grafiek weer. Als u boven de grafiek zweeft, wordt de waarde op dat punt weergegeven.

Elk gegevenspunt in de grafiek is een aggregaat van de gegevenswaarden die zijn ontvangen in het voorafgaande bemonsteringsinterval of "granulariteit". De granulariteit wordt boven aan het blad weergegeven en varieert met de totale tijdschaal van de grafiek.

Statistieken kunnen op verschillende manieren worden samengevoegd:

* **Aantal** is een telling van de gebeurtenissen die zijn ontvangen in het bemonsteringsinterval. Het wordt gebruikt voor gebeurtenissen zoals aanvragen. Variaties in de hoogte van de grafiek geven variaties aan in de snelheid waarmee de gebeurtenissen optreden. Houd er echter rekening mee dat de numerieke waarde verandert wanneer u het bemonsteringsinterval wijzigt.
* **Som** telt de waarden op van alle gegevenspunten die tijdens het bemonsteringsinterval of de periode van de grafiek zijn ontvangen.
* **Gemiddelde** verdeelt de som door het aantal gegevenspunten dat over het interval is ontvangen.
* **Unieke** tellingen worden gebruikt voor het tellen van gebruikers en accounts. Tijdens het bemonsteringsinterval of gedurende de periode van de grafiek toont de figuur het aantal verschillende gebruikers dat in die tijd is gezien.
* **%**- percentageversies van elke aggregatie worden alleen gebruikt bij gesegmenteerde grafieken. Het totaal komt altijd op 100% en de grafiek toont de relatieve bijdrage van verschillende componenten van een totaal.

    ![Percentage aggregatie](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Het aggregatietype wijzigen

![De grafiek bewerken en vervolgens Aggregatie selecteren](./media/metrics-explorer/05-aggregation.png)

De standaardmethode voor elke statistiek wordt weergegeven wanneer u een nieuwe grafiek maakt of wanneer alle statistieken zijn uitgeschakeld:

![Schakel alle statistieken uit om de standaardwaarden weer te geven](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Y-as vastmaken 
Standaard worden in een grafiek de Waarden van de Y-as weergegeven die beginnen van nul tot maximale waarden in het gegevensbereik, om een visuele weergave van quantum van de waarden te geven. Maar in sommige gevallen meer dan de quantum kan het interessant zijn om visueel te inspecteren kleine veranderingen in waarden. Voor aanpassingen zoals deze gebruik maken van de Y-as bereik bewerken functie om de Y-as minimum of maximale waarde op de gewenste plaats vast te maken.
Klik op het selectievakje 'Geavanceerde instellingen' om de instellingen voor het Y-asbereik weer te geven

![Klik op Geavanceerde instellingen, selecteer Aangepast bereik en geef min-maximumwaarden op](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Uw gegevens filteren
Ga als u alleen de statistieken bekijken voor een geselecteerde set eigenschapswaarden:

![Klik op Filteren, vouw een eigenschap uit en controleer enkele waarden](./media/metrics-explorer/19-filter.png)

Als u geen waarden selecteert voor een bepaalde eigenschap, is dit hetzelfde als ze allemaal selecteren: er is geen filter op die eigenschap.

Let op de tellingen van gebeurtenissen naast elke eigenschapswaarde. Wanneer u waarden van één eigenschap selecteert, worden de tellingen naast andere eigenschapswaarden aangepast.

Filters zijn van toepassing op alle grafieken op een blad. Als u verschillende filters wilt toepassen op verschillende grafieken, maakt en slaat u verschillende metrische bladen op. Als u wilt, u grafieken van verschillende bladen naar het dashboard vastmaken, zodat u ze naast elkaar zien.

### <a name="remove-bot-and-web-test-traffic"></a>Bot- en webtestverkeer verwijderen
Gebruik het filter **Echt of synthetisch verkeer** en controleer **Real**.

U ook filteren op **bron van synthetisch verkeer.**

### <a name="to-add-properties-to-the-filter-list"></a>Eigenschappen toevoegen aan de filterlijst
Wilt u telemetrie filteren op een categorie naar eigen keuze? Misschien deelt u uw gebruikers bijvoorbeeld op in verschillende categorieën en wilt u uw gegevens segmenteren naar deze categorieën.

[Maak uw eigen woning.](../../azure-monitor/app/api-custom-events-metrics.md#properties) Stel het in een [Telemetrie Initializer](../../azure-monitor/app/api-custom-events-metrics.md#defaults) om het te laten verschijnen in alle telemetrie - met inbegrip van de standaard telemetrie verzonden door verschillende SDK-modules.

## <a name="edit-the-chart-type"></a>Het grafiektype bewerken
U schakelen tussen rasters en grafieken:

![Selecteer een raster of grafiek en kies vervolgens een grafiektype](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Sla uw metrische gegevensblad op
Wanneer u een aantal grafieken hebt gemaakt, slaat u ze op als favoriet. U kiezen of u het wilt delen met andere teamleden als u een organisatieaccount gebruikt.

![Favoriet kiezen](./media/metrics-explorer/21-favorite-save.png)

Ga naar het **overzichtsblad en** open Favorieten om het mes opnieuw te zien:

![Kies Favorieten in het blad Overzicht](./media/metrics-explorer/22-favorite-get.png)

Als u relatief tijdsbereik hebt gekozen wanneer u het blad hebt opgeslagen, wordt het blad bijgewerkt met de nieuwste statistieken. Als u absolute tijdsbereik hebt gekozen, worden elke keer dezelfde gegevens weergegeven.

## <a name="reset-the-blade"></a>Het blad opnieuw instellen
Als u een blade bewerkt, maar u wilt dan terug naar de oorspronkelijke opgeslagen set, klikt u op Opnieuw instellen.

![In de knoppen boven aan Metric Explorer](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Live statistieken stream

Open [Live Stream](live-stream.md)voor een veel directer overzicht van uw telemetrie. De meeste statistieken duren een paar minuten om te verschijnen, vanwege het proces van aggregatie. Live metrics zijn daarentegen geoptimaliseerd voor lage latentie. 

## <a name="set-alerts"></a>Waarschuwingen instellen
Als u per e-mail op de hoogte wilt worden gesteld van ongebruikelijke waarden van een statistiek, voegt u een waarschuwing toe. U ervoor kiezen om de e-mail naar de accountbeheerders of naar specifieke e-mailadressen te sturen.

![Kies in Metrics Explorer Waarschuwingsregels, Waarschuwing toevoegen](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[Meer informatie over waarschuwingen][alerts].


## <a name="continuous-export"></a>Continue export
Als u gegevens continu wilt exporteren, zodat u deze extern verwerken, u overwegen [om Continu exporteren te gebruiken.](../../azure-monitor/app/export-telemetry.md)

### <a name="power-bi"></a>Power BI
Als u nog rijkere weergaven van uw gegevens wilt, u [exporteren naar Power BI.](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)

## <a name="analytics"></a>Analyse
[Analytics](../../azure-monitor/app/analytics.md) is een meer veelzijdige manier om uw telemetrie te analyseren met behulp van een krachtige querytaal. Gebruik het als u resultaten van statistieken wilt combineren of berekenen of een diepgaande verkenning van de recente prestaties van uw app wilt uitvoeren. 

Vanuit een metrische grafiek u op het analytics-pictogram klikken om rechtstreeks naar de gelijkwaardige Analytics-query te gaan.

## <a name="troubleshooting"></a>Problemen oplossen
*Ik zie geen gegevens op mijn kaart.*

* Filters zijn van toepassing op alle grafieken op het blad. Zorg ervoor dat u tijdens de focus op één grafiek geen filter hebt ingesteld dat alle gegevens op een andere kaart uitsluit.

    Als u verschillende filters op verschillende grafieken wilt instellen, maakt u ze in verschillende bladen en slaat u ze op als afzonderlijke favorieten. Als u wilt, u ze vastmaken aan het dashboard, zodat u ze naast elkaar zien.
* Als u een grafiek groepeert met een eigenschap die niet is gedefinieerd op de statistiek, staat er niets in de grafiek. Probeer 'groeperen op' te wissen of kies een andere eigenschap voor groepering.
* Prestatiegegevens (CPU, IO-snelheid, enzovoort) zijn beschikbaar voor Java-webservices, Windows-bureaubladapps, [IIS-webapps en -services als u statusmonitor installeert](../../azure-monitor/app/monitor-performance-live-website-now.md)en [Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md). Het is niet beschikbaar voor Azure-websites.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Volgende stappen
* [Gebruik controleren met Application Insights](../../azure-monitor/app/usage-overview.md)
* [Diagnostisch zoeken gebruiken](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
