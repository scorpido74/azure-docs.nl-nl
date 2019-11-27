---
title: Gegevens verkennen met behulp van de Explorer-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het gebruik van de Azure Time Series Insights Explorer om uw IoT-gegevens weer te geven.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/20/2019
ms.custom: seodec18
ms.openlocfilehash: f6fafb2d4d363ee0d01d5bd9f18a1294ae8110b7
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327731"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights Explorer

In dit artikel worden de functies en opties in algemene Beschik baarheid voor de [Web-app](https://insights.timeseries.azure.com/)van Azure time series Insights Explorer beschreven. De Time Series Insights Explorer demonstreert de krachtige functies voor gegevens visualisatie die door de service worden geboden en kunnen worden geopend in uw eigen omgeving.

Azure Time Series Insights is een volledig beheerde service voor analyse, opslag en visualisatie die het eenvoudig maakt om miljarden IoT-gebeurtenissen tegelijkertijd te verkennen en te analyseren. Het biedt u een wereld wijde weer gave van uw gegevens, waarmee u uw IoT-oplossing snel kunt valideren en dure uitval tijd kunt vermijden voor bedrijfskritische apparaten. U kunt verborgen trends ontdekken, afwijkings afwijkingen en hoofd oorzaken van analyses in vrijwel realtime uitvoeren. De Time Series Insights Explorer is momenteel beschikbaar als open bare preview.

> [!TIP]
> Lees de [Azure time series Insights Snelstartgids](time-series-quickstart.md)voor een rond leiding door de demonstratie omgeving.

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Meer informatie over het opvragen van gegevens met behulp van de Time Series Insights Explorer. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Zie de voor gaande video <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">' aan de slag met Time Series Insights met een Azure IOT-oplossings versneller '.</a>

## <a name="prerequisites"></a>Vereisten

Voordat u Time Series Insights Explorer kunt gebruiken, moet u het volgende doen:

- Maak een Time Series Insights-omgeving. Zie hoe u aan de [slag gaat met Time Series Insights](./time-series-insights-get-started.md)voor meer informatie.
- [Bied toegang](time-series-insights-data-access.md) tot uw account in de omgeving.
- Voeg een [IOT-hub](time-series-insights-how-to-add-an-event-source-iothub.md) of [Event hub](time-series-insights-how-to-add-an-event-source-eventhub.md) gebeurtenis bron toe.

## <a name="explore-and-query-data"></a>Gegevens verkennen en opvragen

Binnen enkele minuten nadat u uw gebeurtenis bron hebt verbonden met uw Time Series Insights-omgeving, kunt u uw time series-gegevens verkennen en opvragen.

1. Open de [Time Series Insights Explorer](https://insights.timeseries.azure.com/) in uw webbrowser om te starten. Selecteer een omgeving aan de linkerkant van het venster. Alle omgevingen waartoe u toegang hebt, worden in alfabetische volg orde weer gegeven.

1. Nadat u een omgeving hebt geselecteerd, gebruikt u de configuraties **van** en **tot aan** de bovenkant of klikt u op de gewenste tijds periode en sleept u deze. Selecteer het vergroot glas in de rechter bovenhoek of klik met de rechter muisknop op de geselecteerde time span en selecteer **zoeken**.

1. U kunt de beschik baarheid ook elke minuut automatisch vernieuwen door de knop **automatisch aan** te selecteren. De knop **automatisch aan** is alleen van toepassing op de beschik baarheids grafiek, niet op de inhoud van de hoofd visualisatie.

1. Met het pictogram van de Azure-Cloud gaat u naar uw omgeving in het Azure Portal.

   [selectie van Time Series Insights omgeving ![](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. Vervolgens ziet u een grafiek met een telling van alle gebeurtenissen tijdens de geselecteerde tijds periode. Hier hebt u een aantal besturings elementen:

    - **Paneel voor termen editor**: de term ruimte is de locatie waar u een query op uw omgeving uitvoert. Deze bevindt zich aan de linkerkant van het scherm:
      - **Meting**: in deze vervolg keuzelijst worden alle numerieke kolommen (**dubbele**waarden) weer gegeven.
      - **Splitsen op**: deze vervolg keuzelijst bevat categorische kolommen (**teken reeksen**).
      - U kunt de stap interpolatie inschakelen, het minimum en maximum weer geven en de y-as van het configuratie scherm naast **meting**aanpassen. U kunt ook aanpassen of de weer gegeven gegevens een aantal, een gemiddelde of een som van de gegevens zijn.
      - U kunt Maxi maal vijf termen toevoegen die op dezelfde x-as worden weer gegeven. Selecteer **toevoegen** om een nieuwe term toe te voegen of gebruik de knop **deze term klonen** om een kopie van een bestaande term toe te voegen.

        [![voor waarden selecteren, filteren en query paneel](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Predikaat**: gebruik het predicaat om snel uw gebeurtenissen te filteren met behulp van de set operands die in de volgende tabel worden weer gegeven. Als u een zoek opdracht uitvoert door te selecteren of te klikken, wordt het predikaat automatisch bijgewerkt op basis van die zoek opdracht. Ondersteunde Operandtypen zijn onder andere:

         |Bewerking  |Ondersteunde typen  |Opmerkingen  |
         |---------|---------|---------|
         |**<** , **>** , **<=** , **>=**    |  **Double**, **DateTime**, **time span**       |         |
         |**=** , **! =** , **<>**     | **Teken reeks**, **BOOL**, **Double**, **DateTime**, **time span**, **Null**        |         |
         |**Naast**     | **Teken reeks**, **BOOL**, **Double**, **DateTime**, **time span**, **Null**        |  Alle operanden moeten van hetzelfde type zijn of **Null** -constante zijn.        |
         |**BIEDT**     | **String**        |  Aan de rechter kant zijn alleen letterlijke teken reeksen van constanten toegestaan. Lege teken reeks en **Null** zijn niet toegestaan.       |

      - **Voorbeeld query's**

         [![-voor beeld GA-query's](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. U kunt de schuif regelaar voor **interval grootte** gebruiken om in en uit te zoomen op dezelfde tijds duur. De schuif regelaar biedt een nauw keurigere controle over de verplaatsing tussen grote tijds segmenten die vloeiende trends weer geven naar segmenten zo klein als de milliseconde, waarmee u nauw keurige delen van uw gegevens met hoge resolutie kunt zien. Het standaard start punt van de schuif regelaar is ingesteld als de meest optimale weer gave van de gegevens uit uw selectie om de balans resolutie, de query snelheid en de granulatie te bekijken.

1. Met het gereedschap **tijd penseel** kunt u eenvoudig navigeren van de ene span naar een andere.

1. Selecteer het pictogram **Opslaan** om de huidige query op te slaan en deze te delen met andere gebruikers van de omgeving. Wanneer u het pictogram **openen** selecteert, ziet u alle opgeslagen query's en alle gedeelde query's van andere gebruikers in omgevingen waar u toegang tot hebt.

   [![Query's](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Gegevens visualiseren

1. Gebruik het hulp programma **perspectief weergave** voor een gelijktijdige weer gave van Maxi maal vier unieke query's. De **perspectief weergave** knop bevindt zich in de rechter bovenhoek van de grafiek.

   [![selectie query's die u aan het deel venster perspectief wilt toevoegen](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Bekijk een grafiek om uw gegevens visueel te verkennen en gebruik de hulp middelen voor **grafieken** :

    - **Selecteer** of **Klik op** een specifieke time span of een enkele gegevens reeks.
    - Binnen een time span-selectie kunt u gebeurtenissen in-en uitzoomen of bekijken.
    - Binnen een gegevens reeks kunt u de reeks opsplitsen in een andere kolom, de reeks toevoegen als nieuwe term, alleen de geselecteerde reeks weer geven, de geselecteerde reeks uitsluiten, de reeks pingen of de gebeurtenissen uit de geselecteerde reeks verkennen.
    - In het filter gebied links van de grafiek ziet u alle weer gegeven gegevens reeksen en vervolgens de volg orde op waarde of naam. U kunt ook alle gegevens reeksen of vastgemaakte of niet-vastgemaakte reeksen weer geven. U kunt één gegevens reeks selecteren en de reeks splitsen op basis van een andere kolom, de reeks toevoegen als nieuwe term, alleen de geselecteerde reeks weer geven, de geselecteerde reeks uitsluiten, de reeks vastmaken of gebeurtenissen uit de geselecteerde reeks verkennen.
    - Wanneer u meerdere voor waarden tegelijk bekijkt, kunt u een stack stapelen, opstapelen, aanvullende gegevens over een gegevens reeks weer geven en dezelfde y-as voor alle termen gebruiken. Gebruik de knoppen in de rechter bovenhoek van de grafiek.

    [opties voor de rechter bovenhoek van het hulp programma grafieken ![](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. Gebruik de **heatmap** om snel unieke of afwijkende gegevens reeksen in een bepaalde query op te sporen. Er kan slechts één zoek term worden gevisualiseerd als een heatmap.

    [![Explorer heatmap-grafiek](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. Wanneer u gebeurtenissen bekijkt door te selecteren of met de rechter muisknop te klikken, wordt het deel venster **gebeurtenissen** beschikbaar gesteld. Hier kunt u al uw onbewerkte gebeurtenissen zien en uw gebeurtenissen exporteren als JSON-of CSV-bestanden. Time Series Insights worden alle onbewerkte gegevens opgeslagen.

    [![gebeurtenissen](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Selecteer het tabblad **Statistieken** nadat u gebeurtenissen hebt bekeken om patronen en kolom statistieken weer te geven.

    - **Patronen**: met deze functie worden de meest statistische significante patronen in een geselecteerd gegevens gebied proactief geoppereerd. U hoeft geen duizenden gebeurtenissen te bekijken om te begrijpen welke patronen de meeste tijd en energie vereisen. Met Time Series Insights kunt u rechtstreeks naar deze statistisch significante patronen gaan om een analyse te blijven uitvoeren. Deze functie is ook handig voor het naslachten van historische gegevens.
    - **Kolom statistieken**: kolom statistieken bieden grafieken en tabellen die gegevens opsplitsen van elke kolom van de geselecteerde gegevens reeks over de geselecteerde time span.

      [kolom diagram en opties voor ![statistieken](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

U hebt nu de verschillende functies en opties zien die beschikbaar zijn in de Web-App van Time Series Insights Explorer.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [vaststellen en oplossen van problemen](time-series-insights-diagnose-and-solve-problems.md) in uw time series Insights omgeving.

- Volg de rond leiding door de begeleide [Azure time series Insights Snelstartgids](time-series-quickstart.md) .
