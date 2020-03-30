---
title: Gegevens verkennen met behulp van de explorer - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over het gebruik van de Azure Time Series Insights explorer om uw IoT-gegevens weer te geven.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/15/2020
ms.custom: seodec18
ms.openlocfilehash: b8c2ba54fcc69ba126bf5f68aed99b25f8156155
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046175"
---
# <a name="azure-time-series-insights-explorer"></a>Insights explorer voor Azure Time Series

In dit artikel worden de functies en opties in algemene beschikbaarheid beschreven voor de [web-app](https://insights.timeseries.azure.com/)Azure Time Series Insights explorer. De Time Series Insights explorer demonstreert de krachtige mogelijkheden voor datavisualisatie die door de service worden geboden en is toegankelijk binnen uw eigen omgeving.

Azure Time Series Insights is een volledig beheerde service voor analyse, opslag en visualisatie die het eenvoudig maakt om miljarden IoT-gebeurtenissen tegelijkertijd te verkennen en te analyseren. Het geeft u een globaal overzicht van uw gegevens, waarmee u uw IoT-oplossing snel valideren en kostbare downtime voor bedrijfskritieke apparaten voorkomen. U verborgen trends ontdekken, afwijkingen herkennen en root-cause analyses uitvoeren in bijna realtime. De Time Series Insights explorer is momenteel in openbare preview.

> [!TIP]
> Lees de [Azure Time Series Insights snel aan](time-series-quickstart.md)de slag voor een rondleiding door de demonstratieomgeving.

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Meer informatie over het opvragen van gegevens met behulp van de Time Series Insights explorer. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Speel de vorige video <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">'Aan de slag met Time Series Insights met behulp van een Azure IoT Solution Accelerator'.</a>

## <a name="prerequisites"></a>Vereisten

Voordat u Time Series Insights explorer gebruiken, moet u het:

- Maak een Time Series Insights-omgeving. Lees voor meer informatie [Hoe aan de slag te gaan met Time Series Insights](./time-series-insights-get-started.md).
- [Geef toegang](time-series-insights-data-access.md) tot uw account in de omgeving.
- Voeg er een [IoT-hub](time-series-insights-how-to-add-an-event-source-iothub.md) of [gebeurtenishubgebeurtenisbron](time-series-insights-how-to-add-an-event-source-eventhub.md) aan toe.

## <a name="explore-and-query-data"></a>Gegevens verkennen en opvragen

Binnen enkele minuten nadat u uw gebeurtenisbron hebt aangesloten op uw Time Series Insights-omgeving, u uw tijdreeksgegevens verkennen en opvragen.

1. Open de [Time Series Insights-verkenner](https://insights.timeseries.azure.com/) in uw webbrowser om te beginnen. Selecteer aan de linkerkant van het venster een omgeving. Alle omgevingen waartoe u toegang hebt, worden in alfabetische volgorde weergegeven.

1. Nadat u een omgeving hebt geselecteerd, gebruikt u de configuraties **Van** en **Aan** bovenaan of selecteert en sleept u over de gewenste tijdspanne. Selecteer het vergrootglas in de rechterbovenhoek of klik met de rechtermuisknop op de geselecteerde tijdspanne en selecteer **Zoeken**.

1. U de beschikbaarheid ook elke minuut automatisch vernieuwen door de knop **Automatisch aan te** selecteren. De knop **Automatisch aan** is alleen van toepassing op het beschikbaarheidsdiagram, niet op de inhoud van de hoofdvisualisatie.

1. Het Azure-cloudpictogram brengt u naar uw omgeving in de Azure-portal.

   [![Time Series Insights-omgevingsselectie](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. Vervolgens wordt een grafiek weergegeven met een telling van alle gebeurtenissen tijdens de geselecteerde tijdspanne. Hier heb je een aantal besturingselementen:

    - **Deelvenster Termeneditor**: De term ruimte is waar u uw omgeving bevraagt. Het is te vinden aan de linkerkant van het scherm:
      - **MAATREGEL**: In deze vervolgkeuzelijst worden alle numerieke kolommen weergegeven (**Doubles**).
      - **SPLIT BY**: Deze vervolgkeuzelijst toont categorische kolommen (**Strings**).
      - U stapsgewijze interpolatie inschakelen, minimum en maximum weergeven en de y-as aanpassen vanuit het bedieningspaneel naast **METEN.** U ook aanpassen of weergegeven gegevens een telling, gemiddelde of som van de gegevens zijn.
      - U maximaal vijf termen toevoegen om op dezelfde x-as te bekijken. Selecteer **Toevoegen** om een nieuwe term toe te voegen of gebruik de knop **Deze term klonen** om een kopie van een bestaande term toe te voegen.

        [![Deelvenster Termen selecteren, filteren en query](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Predicaat:** Gebruik het predicaat om uw gebeurtenissen snel te filteren met behulp van de set operands in de volgende tabel. Als u een zoekopdracht uitvoert door te selecteren of te klikken, wordt het predicaat automatisch bijgewerkt op basis van die zoekopdracht. Ondersteunde operand types zijn:

         |Bewerking  |Ondersteunde typen  |Opmerkingen  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Dubbel**, **DateTime**, **TimeSpan**       |         |
         |**=**, **!=**,**<>**     | **String**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |         |
         |**IN**     | **String**, **Bool**, **Double**, **DateTime**, **TimeSpan**, **NULL**        |  Alle operands moeten van hetzelfde type zijn of **NULL-constant** zijn.        |
         |**Hsa**     | **Tekenreeks**        |  Alleen constante string liters zijn toegestaan aan de rechterkant. Lege tekenreeks en **NULL** zijn niet toegestaan.       |

      - **Voorbeelden van query's**

         [![Voorbeeld GA-query's](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. U het schuifregelaar **gereedschap Intervalgrootte** gebruiken om in en uit intervallen in dezelfde tijdspanne in en uit te zoomen. De schuifregelaar biedt een nauwkeurigere controle over de beweging tussen grote segmenten van de tijd die vloeiende trends weergeven tot segmenten zo klein als de milliseconde, waarmee u gedetailleerde delen van uw gegevens met hoge resolutie weergeven en analyseren. Het standaardbeginpunt van de schuifregelaar is ingesteld als de meest optimale weergave van de gegevens uit uw selectie om de resolutie, querysnelheid en granulariteit in evenwicht te brengen.

1. Met het penseel **Tijd** u eenvoudig van de ene tijdspanne naar de andere navigeren.

1. Selecteer het pictogram **Opslaan** om uw huidige query op te slaan en deze te delen met andere gebruikers van de omgeving. Wanneer u het pictogram **Openen** selecteert, u alle opgeslagen query's en gedeelde query's van andere gebruikers bekijken in omgevingen waartoe u toegang hebt.

   [![Query 's](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Gegevens visualiseren

1. Gebruik het gereedschap **Perspectiefweergave** voor een gelijktijdige weergave van maximaal vier unieke query's. De knop **Perspectiefweergave** bevindt zich in de rechterbovenhoek van de grafiek.

   [![Query's selecteren die u wilt toevoegen aan het perspectiefvenster](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Bekijk een grafiek om uw gegevens visueel te verkennen en gebruik de **hulpmiddelen voor Grafieken:**

    - **Selecteer** of **klik op** een specifieke tijdspanne of een enkele gegevensreeks.
    - Binnen een tijdspanne selectie, u inzoomen of verkennen van evenementen.
    - Binnen een gegevensreeks u de reeks splitsen door een andere kolom, de reeks toevoegen als een nieuwe term, alleen de geselecteerde reeks weergeven, de geselecteerde reeks uitsluiten, die reeks pingen of gebeurtenissen uit de geselecteerde reeks verkennen.
    - In het filtergebied links van de grafiek u alle weergegeven gegevensreeksen bekijken en de volgorde op waarde of naam wijzigen. U ook alle gegevensreeksen of een vastgemaakte of niet-vastgemaakte reeks bekijken. U één gegevensreeks selecteren en de reeks splitsen door een andere kolom, de reeks toevoegen als een nieuwe term, alleen de geselecteerde reeks weergeven, de geselecteerde reeks uitsluiten, die reeks vastmaken of gebeurtenissen uit de geselecteerde reeks verkennen.
    - Wanneer u meerdere termen tegelijk bekijkt, u de stapel stapelen, extra gegevens over een gegevensreeks bekijken en dezelfde y-as gebruiken voor alle termen. Gebruik de knoppen in de rechterbovenhoek van de grafiek.

    [![Instellingen voor de optie -grafiekrecht](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. Gebruik de **heatmap** om snel unieke of afwijkende gegevensreeksen in een bepaalde query te herkennen. Slechts één zoekterm kan worden gevisualiseerd als een heatmap.

    [![GA explorer heatmap grafieken](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png#lightbox)

1. Wanneer u gebeurtenissen verkent door te selecteren of met de rechtermuisknop te klikken, wordt het deelvenster **GEBEURTENISSEN** beschikbaar gesteld. Hier u al uw onbewerkte gebeurtenissen bekijken en uw evenementen exporteren als JSON- of CSV-bestanden. Time Series Insights slaat alle ruwe gegevens op.

    [![Gebeurtenissen](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Selecteer het tabblad **STATISTIEKEN** nadat u gebeurtenissen hebt verkend om patronen en kolomstatistieken bloot te leggen.

    - **Patronen:** Deze functie geeft proactief de meest statistisch significante patronen in een geselecteerd gegevensgebied weer. Je hoeft niet te kijken naar duizenden gebeurtenissen om te begrijpen welke patronen de meeste tijd en energie vereisen. Met Time Series Insights u rechtstreeks in deze statistisch significante patronen springen om een analyse te blijven uitvoeren. Deze functie is ook handig voor postmortem onderzoeken naar historische gegevens.
    - **Kolomstatistieken**: Kolomstatistieken bieden grafieken en tabellen die gegevens uit elke kolom van de geselecteerde gegevensreeks over de geselecteerde tijdspanne opsplitsen.

      [![STATISTIEKEN kolom grafieken en opties](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

Nu hebt u meer te weten gekomen over de belangrijkste functies, configuratie-instellingen en weergaveopties die beschikbaar zijn in de web-app Van Insights explorer uit de tijdreeks.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [diagnosticeren en oplossen van problemen](time-series-insights-diagnose-and-solve-problems.md) in uw Time Series Insights-omgeving.

- Volg de begeleide [Azure Time Series Insights quickstart-tour.](time-series-quickstart.md)
