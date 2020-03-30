---
title: Waarschuwingen maken met dynamische drempels in Azure-monitor
description: Waarschuwingen maken met op machine learning gebaseerde dynamische drempels
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 9345138e948d84e0ea3c804dbd7a4b3c21daca2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668142"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Metrische waarschuwingen met dynamische drempelwaarden in Azure-monitor

Metric Alert met dynamic thresholds-detectie maakt gebruik van geavanceerde machine learning (ML) om het historische gedrag van statistieken te leren kennen, patronen en afwijkingen te identificeren die mogelijke serviceproblemen aangeven. Het biedt ondersteuning van zowel een eenvoudige gebruikersinterface als bewerkingen op schaal door gebruikers in staat te stellen waarschuwingsregels te configureren via de Azure Resource Manager API, op een volledig geautomatiseerde manier.

Zodra een waarschuwingsregel is gemaakt, wordt deze alleen mogelijk afgemaakt wanneer de bewaakte statistiek zich niet gedraagt zoals verwacht, op basis van de op maat gemaakte drempelwaarden.

We horen graag uw feedback, houden <azurealertsfeedback@microsoft.com>het komen op .

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Waarom en wanneer wordt het gebruik van dynamische conditie type aanbevolen?

1. **Scalable Alerting** – Dynamische drempelwaarschuwingsregels kunnen op maat gemaakte drempelwaarden maken voor honderden metrische reeksen tegelijk, maar bieden hetzelfde gemak van het definiëren van een waarschuwingsregel op één statistiek. Ze geven u minder alert om te maken en te beheren. U Azure-portal of de Azure Resource Manager-API gebruiken om ze te maken. De schaalbare aanpak is vooral handig bij het omgaan met metrische dimensies of bij het toepassen op meerdere resources, zoals voor alle abonnementsbronnen.  [Meer informatie over het configureren van metrische waarschuwingen met dynamische drempels met sjablonen](alerts-metric-create-templates.md).

1. **Smart Metric Pattern Recognition** - Met behulp van onze ML-technologie kunnen we automatisch metrische patronen detecteren en ons aanpassen aan metrische veranderingen in de tijd, die vaak seizoensgebondenheid (per uur / dagelijks / wekelijks) kunnen omvatten. Aanpassing aan het gedrag van de statistieken in de loop van de tijd en waarschuwingen op basis van afwijkingen van het patroon verlicht de last van het kennen van de "juiste" drempel voor elke statistiek. Het ML-algoritme dat wordt gebruikt in Dynamische drempels is ontworpen om luidruchtige (lage precisie) of brede (lage terugroep)-drempels te voorkomen die geen verwacht patroon hebben.

1. **Intuïtieve configuratie** – Met dynamische drempels u metrische waarschuwingen instellen met concepten op hoog niveau, waardoor u geen uitgebreide domeinkennis over de statistiek hoeft te hebben.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Hoe configureer je waarschuwingsregels met dynamische drempelwaarden?

Waarschuwingen met dynamische drempelwaarden kunnen worden geconfigureerd via metrische waarschuwingen in Azure Monitor. [Meer informatie over het configureren van metrische waarschuwingen](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Hoe worden de drempels berekend?

Dynamic Thresholds leert voortdurend de gegevens van de metrische reeks en probeert deze te modelleren met behulp van een reeks algoritmen en methoden. Het detecteert patronen in de gegevens, zoals seizoensgebondenheid (Per uur / dagelijks / wekelijks), en is in staat om luidruchtige statistieken (zoals machine CPU of geheugen) evenals statistieken met een lage spreiding (zoals beschikbaarheid en foutenpercentage) te behandelen.

De drempelwaarden worden zo geselecteerd dat een afwijking van deze drempelwaarden wijst op een afwijking in het metrische gedrag.

> [!NOTE]
> Seizoenspatroondetectie is ingesteld op een interval van een uur, dag of week. Dit betekent dat andere patronen, zoals bihourly patroon of semiweekly misschien niet worden gedetecteerd.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Wat betekent 'Gevoeligheid' instelling in Dynamische drempels?

Gevoeligheid voor waarschuwingsdrempelis een concept op hoog niveau dat de hoeveelheid afwijking van metriek gedrag regelt die nodig is om een waarschuwing te activeren.
Deze optie vereist geen domeinkennis over de metrische als statische drempelwaarde. De beschikbare opties zijn:

- Hoog – De drempelwaarden zijn strak en dicht bij het patroon van de metrische reeks. Een waarschuwingsregel wordt geactiveerd bij de kleinste afwijking, wat resulteert in meer waarschuwingen.
- Medium – Minder strakke en meer evenwichtige drempels, minder waarschuwingen dan bij hoge gevoeligheid (standaard).
- Laag : de drempels zijn los met meer afstand tot het patroon van metrische reeksen. Een waarschuwingsregel leidt alleen tot grote afwijkingen, wat resulteert in minder waarschuwingen.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Wat zijn de instellingsopties 'Operator' in Dynamische drempels?

De waarschuwingsregel dynamische drempelwaarden kan op maat gemaakte drempelwaarden maken op basis van metrisch gedrag voor zowel boven- als ondergrenzen met dezelfde waarschuwingsregel.
U de waarschuwing kiezen die moet worden geactiveerd op een van de volgende drie voorwaarden:

- Groter dan de bovendrempel of lager dan de onderdrempel (standaard)
- Groter dan de bovengrens
- Lager dan de ondergrens.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Wat betekenen de geavanceerde instellingen in Dynamische drempels?

**Niet-perioden** - Dynamische drempels u ook configureren Aantal schendingen om de waarschuwing te activeren, een minimum aantal afwijkingen die nodig zijn binnen een bepaald tijdvenster voor het systeem om een waarschuwing te verhogen (het standaardtijdvenster is vier afwijkingen in 20 minuten). De gebruiker kan falende perioden configureren en kiezen waar u op moet worden gewaarschuwd door de ontbrekende perioden en het tijdvenster te wijzigen. Deze mogelijkheid vermindert waarschuwingsruis gegenereerd door tijdelijke pieken. Bijvoorbeeld:

Als u een waarschuwing wilt activeren wanneer het probleem gedurende 20 minuten is onderbroken, gebruikt u 4 opeenvolgende keren in een bepaalde periode een groepering van 5 minuten:

![Falende perioden instellingen voor continue probleem gedurende 20 minuten, 4 opeenvolgende keer in een bepaalde periode groepering van 5 minuten](media/alerts-dynamic-thresholds/0008.png)

Als u een waarschuwing wilt activeren wanneer er een schending van een dynamische drempelwaarde in 20 minuten van de laatste 30 minuten met een periode van 5 minuten is gemaakt, gebruikt u de volgende instellingen:

![Falende perioden instellingen voor probleem voor 20 minuten van de laatste 30 minuten met periode groepering van 5 minuten](media/alerts-dynamic-thresholds/0009.png)

**Gegevens eerder negeren** - Gebruikers kunnen ook optioneel een begindatum definiëren van waaruit het systeem moet beginnen met het berekenen van de drempelwaarden. Een typische use case kan optreden wanneer een resource een run was in een testmodus en nu wordt gepromoot om een productiewerkbelasting te serveren, en daarom moet het gedrag van elke statistiek tijdens de testfase worden genegeerd.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Hoe kom je erachter waarom een Dynamic Thresholds alert is geactiveerd?

U geactiveerde waarschuwingsinstanties in de waarschuwingsweergave verkennen door op de koppeling in het e-mailbericht of in het sms-bericht te klikken of door de waarschuwingsweergave in de Azure-portal weer te geven. [Meer informatie over de weergave waarschuwingen](alerts-overview.md#alerts-experience).

De waarschuwingsweergave wordt weergegeven:

- Alle metrische details op het moment dat de dynamic thresholds alert is geactiveerd.
- Een grafiek van de periode waarin de waarschuwing is geactiveerd en die de dynamische drempelwaarden bevat die op dat moment worden gebruikt.
- Mogelijkheid om feedback te geven over dynamische drempelwaarden alert en de waarschuwingen weergave ervaring, die toekomstige detecties zou kunnen verbeteren.

## <a name="will-slow-behavior-changes-in-the-metric-trigger-an-alert"></a>Activeren langzame gedragswijzigingen in de statistiek een waarschuwing?

Waarschijnlijk niet. Dynamische drempels zijn goed voor het detecteren van significante afwijkingen in plaats van langzaam evoluerende problemen.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Hoeveel gegevens worden gebruikt om drempels te bekijken en vervolgens te berekenen?

Wanneer een waarschuwingsregel voor het eerst wordt gemaakt, worden de drempelwaarden in de grafiek berekend op basis van voldoende historische gegevens om uur- of dagelijkse seizoenspatronen (10 dagen) te berekenen. Zodra een waarschuwingsregel is gemaakt, gebruikt Dynamische drempels alle benodigde historische gegevens die beschikbaar zijn en zullen ze voortdurend leren en aanpassen op basis van nieuwe gegevens om de drempels nauwkeuriger te maken. Dit betekent dat na deze berekening de grafiek ook wekelijkse patronen weergeeft.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Hoeveel gegevens zijn er nodig om een waarschuwing te activeren?

Als u een nieuwe bron of ontbrekende metrische gegevens hebt, worden er geen waarschuwingen geactiveerd voordat drie dagen of 30 monsters met metrische gegevens beschikbaar zijn om nauwkeurige drempelwaarden te garanderen.

## <a name="dynamic-thresholds-best-practices"></a>Aanbevolen procedures voor dynamische drempels

Dynamische drempels kunnen worden toegepast op elk platform of aangepaste statistiek in Azure Monitor en het is ook afgestemd op de algemene toepassings- en infrastructuurstatistieken.
De volgende items zijn aanbevolen procedures voor het configureren van waarschuwingen voor sommige van deze statistieken met behulp van dynamische drempelwaarden.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Dynamische drempelwaarden voor cpu-procentuele statistieken voor virtuele machines

1. Klik in [Azure-portal](https://portal.azure.com)op **Monitor**. De monitorweergave consolideert al uw bewakingsinstellingen en gegevens in één weergave.

2. Klik **op Waarschuwingen** en klik **op + Nieuwe waarschuwingsregel**.

    > [!TIP]
    > De meeste resource blades hebben ook **Waarschuwingen** in hun resource menu onder **Monitoring**, je zou kunnen maken waarschuwingen vanaf daar ook.

3. Klik op **Doel selecteren**, selecteer in het contextvenster dat wordt geladen een doelbron waarop u wilt waarschuwen. **Vervolgkeuzelijst voor abonnements-** en **'Virtuele machines' resourcetypen** gebruiken om de bron te vinden die u wilt controleren. U ook de zoekbalk gebruiken om uw bron te vinden.

4. Nadat u een doelbron hebt geselecteerd, klikt u op **Voorwaarde toevoegen**.

5. Selecteer het **'CPU-percentage'.**

6. Verfijn de statistiek eventueel door **de periode** en **aggregatie**aan te passen . Het wordt afgeraden om 'Maximum' aggregatietype te gebruiken voor dit metrische type omdat het minder representatief is voor gedrag. Voor 'Maximum' aggregatie type statische drempel misschien meer geschikt.

7. U ziet een grafiek voor de statistiek van de afgelopen 6 uur. Definieer de waarschuwingsparameters:
    1. **Voorwaardetype** - Kies de optie 'Dynamisch'.
    1. **Gevoeligheid** - Kies gemiddelde/lage gevoeligheid om waarschuwingsgeluiden te verminderen.
    1. **Operator** - Kies 'Groter dan' tenzij het gedrag het gebruik van de toepassing vertegenwoordigt.
    1. **Frequentie** - Overweeg te verlagen op basis van de bedrijfsimpact van de waarschuwing.
    1. **Niet-perioden** (geavanceerde optie) - Het venster terugkijken moet ten minste 15 minuten duren. Als de periode bijvoorbeeld is ingesteld op vijf minuten, moeten mislukte perioden ten minste drie of meer zijn.

8. In de metrische grafiek worden de berekende drempelwaarden weergegeven op basis van recente gegevens.

9. Klik op **Gereed**.

10. Vul **waarschuwingsgegevens** in, zoals **naam van de waarschuwingsregel**, **beschrijving**en **ernst**.

11. Voeg een actiegroep toe aan de waarschuwing door een bestaande actiegroep te selecteren of een nieuwe actiegroep te maken.

12. Klik **op Gereed** om de metrische waarschuwingsregel op te slaan.

> [!NOTE]
> Metrische waarschuwingsregels die via portal zijn gemaakt, worden gemaakt in dezelfde resourcegroep als de doelbron.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Dynamische drempelwaarden voor http-aanvraaguitvoeringstijd van Application Insights

1. Klik in [Azure-portal](https://portal.azure.com)op **Monitor**. De monitorweergave consolideert al uw bewakingsinstellingen en gegevens in één weergave.

2. Klik **op Waarschuwingen** en klik **op + Nieuwe waarschuwingsregel**.

    > [!TIP]
    > De meeste resource blades hebben ook **Waarschuwingen** in hun resource menu onder **Monitoring**, je zou kunnen maken waarschuwingen vanaf daar ook.

3. Klik op **Doel selecteren**, selecteer in het contextvenster dat wordt geladen een doelbron waarop u wilt waarschuwen. **Vervolgkeuzelijst Voor het** type Resource abonnement en **'Toepassingsinzichten' gebruiken** om de bron te vinden die u wilt controleren. U ook de zoekbalk gebruiken om uw bron te vinden.

4. Nadat u een doelbron hebt geselecteerd, klikt u op **Voorwaarde toevoegen**.

5. Selecteer de **uitvoeringstijd 'HTTP-aanvraag'**.

6. Verfijn de statistiek eventueel door **de periode** en **aggregatie**aan te passen . Het wordt afgeraden om 'Maximum' aggregatietype te gebruiken voor dit metrische type omdat het minder representatief is voor gedrag. Voor 'Maximum' aggregatie type statische drempel misschien meer geschikt.

7. U ziet een grafiek voor de statistiek van de afgelopen 6 uur. Definieer de waarschuwingsparameters:
    1. **Voorwaardetype** - Kies de optie 'Dynamisch'.
    1. **Operator** - Kies 'Groter dan' om waarschuwingen te verminderen die worden geactiveerd bij een verbetering van de duur.
    1. **Frequentie** - Overweeg te verlagen op basis van de bedrijfsimpact van de waarschuwing.

8. In de metrische grafiek worden de berekende drempelwaarden weergegeven op basis van recente gegevens.

9. Klik op **Gereed**.

10. Vul **waarschuwingsgegevens** in, zoals **naam van de waarschuwingsregel**, **beschrijving**en **ernst**.

11. Voeg een actiegroep toe aan de waarschuwing door een bestaande actiegroep te selecteren of een nieuwe actiegroep te maken.

12. Klik **op Gereed** om de metrische waarschuwingsregel op te slaan.

> [!NOTE]
> Metrische waarschuwingsregels die via portal zijn gemaakt, worden gemaakt in dezelfde resourcegroep als de doelbron.

## <a name="interpreting-dynamic-threshold-charts"></a>Dynamische drempeldiagrammen interpreteren

Hieronder volgt een grafiek met een statistiek, de dynamische drempelwaarden en sommige waarschuwingen die worden geactiveerd wanneer de waarde buiten de toegestane drempelwaarden viel.

![Meer informatie over het configureren van metrische waarschuwingen](media/alerts-dynamic-thresholds/threshold-picture-8bit.png)

Gebruik de volgende informatie om de vorige grafiek te interpreteren.

- **Blauwe lijn** - De werkelijke gemeten metrische in de tijd.
- **Blauw gearceerd gebied** - Geeft het toegestane bereik voor de statistiek weer. Zolang de metrische waarden binnen dit bereik blijven, vindt er geen waarschuwing plaats.
- **Blauwe stippen** - Als u links op een deel van de grafiek klikt en vervolgens over de blauwe lijn zweeft, ziet u een blauwe stip onder de cursor verschijnen met een afzonderlijke geaggregeerde metrische waarde.
- **Pop-up met blauwe stip** - Geeft de gemeten metrische waarde (de blauwe stip) en de bovenste en onderste waarden van het toegestane bereik weer.  
- **Rode stip met een zwarte cirkel** - Geeft de eerste metrische waarde weer buiten het toegestane bereik. Dit is de waarde die een metrische waarschuwing afvuurt en deze in een actieve status plaatst.
- **Rode stippen**- Geef extra gemeten waarden aan buiten het toegestane bereik. Ze zullen geen extra metrische waarschuwingen afvuren, maar de waarschuwing blijft actief.
- **Rood gebied** - geeft de tijd weer waarop de metrische waarde zich buiten het toegestane bereik bevond. De waarschuwing blijft in de actieve status zolang de volgende gemeten waarden buiten het toegestane bereik vallen, maar er geen nieuwe waarschuwingen worden geactiveerd.
- **Einde van rood gebied** - Wanneer de blauwe lijn zich weer binnen de toegestane waarden bevindt, stopt het rode gebied en wordt de gemeten waardelijn blauw. De status van de metrische waarschuwing die wordt geactiveerd op het moment van de rode stip met zwarte omtrek is ingesteld op opgelost. 
