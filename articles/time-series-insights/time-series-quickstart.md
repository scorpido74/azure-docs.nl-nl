---
title: 'Snelstart: Azure Time Series Insights explorer - Azure Time Series Insights | Microsoft Documenten'
description: Meer informatie over hoe u aan de slag met de Azure Time Series Insights-verkenner. Visualiseer grote hoeveelheden IoT-gegevens en tour belangrijke functies van uw omgeving.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 01/06/2020
ms.openlocfilehash: a905054b1b2a04fa2b7865d2c1065ccee37cffc0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75860427"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Quickstart: Azure Time Series Insights verkennen

Deze Azure Time Series Insights explorer helpt u aan de slag te gaan met Time Series Insights in een gratis demonstratieomgeving. In deze quickstart leert u hoe u uw webbrowser gebruiken om grote hoeveelheden IoT-gegevens te visualiseren en belangrijke functies te touren die nu algemeen beschikbaar zijn.

Azure Time Series Insights is een volledig beheerde analyse-, opslag- en visualisatieservice die vereenvoudigt hoe u miljarden IoT-gebeurtenissen tegelijk verkennen en analyseren. Het geeft u een globaal overzicht van uw gegevens, zodat u uw IoT-oplossing snel valideren en kostbare downtime voor bedrijfskritieke apparaten voorkomen. Azure Time Series Insights helpt u om verborgen trends te ontdekken, afwijkingen te detecteren en root-cause analyses in bijna realtime uit te voeren.

Voor extra flexibiliteit u Azure Time Series Insights toevoegen aan een reeds bestaande toepassing via de krachtige [REST API's](./time-series-insights-update-tsq.md) en [client SDK.](https://github.com/microsoft/tsiclient) U de API's gebruiken om tijdreeksgegevens op te slaan, op te vragen en te gebruiken in een clienttoepassing naar keuze. U de client-SDK ook gebruiken om gebruikersinterfacecomponenten toe te voegen aan uw bestaande toepassing.

Deze Time Series Insights explorer biedt quickstart een rondleiding langs functies die algemeen beschikbaar zijn.

> [!IMPORTANT]
> Maak een [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) als u er nog geen hebt gemaakt.

## <a name="prepare-the-demo-environment"></a>De demoomgeving voorbereiden

1. Ga in uw browser naar de [algemene beschikbaarheidsdemo.](https://insights.timeseries.azure.com/demo)

1. Meld u desgevraagd aan bij de Time Series Insights explorer met uw Azure-accountreferenties.

1. De quick tourpagina van Time Series Insights wordt weergegeven. Selecteer **Volgende** om de snelle rondleiding te starten.

   [![Quickstart welkom - selecteer Volgende](media/quickstart/quickstart-welcome.png)](media/quickstart/quickstart-welcome.png#lightbox)

## <a name="explore-the-demo-environment"></a>Ontdek de demo-omgeving

1. Het **selectiepaneel Tijd** wordt weergegeven. Gebruik dit venster om het tijdsbestek te selecteren dat u wilt visualiseren.

   [![Venster Time selection](media/quickstart/quickstart-time-selection-panel.png)](media/quickstart/quickstart-time-selection-panel.png#lightbox)

1. Selecteer een tijdsbestek en sleep het in de regio. Selecteer vervolgens **Zoeken**.

   [![Een tijdsbestek selecteren](media/quickstart/quickstart-select-time.png)](media/quickstart/quickstart-select-time.png#lightbox)

   U ziet in Time Series Insights een grafiek met een visualisatie voor het opgegeven tijdsbestek. U verschillende acties uitvoeren in het lijndiagram. U bijvoorbeeld filteren, pinnen, sorteren en stapelen.

   Als u wilt terugkeren naar het **selectiepaneel Tijd,** selecteert u de pijl-omlaag zoals weergegeven:

   [![Grafiek](media/quickstart/quickstart-select-down-arrow.png)](media/quickstart/quickstart-select-down-arrow.png#lightbox)

1. Selecteer **Toevoegen** in het **deelvenster Termen** om een nieuwe zoekterm toe te voegen.

   [![Deelvenster Zoektermen toevoegen](media/quickstart/quickstart-add-terms.png)](media/quickstart/quickstart-add-terms.png#lightbox)

1. In de grafiek kunt u een regio selecteren, hier met de rechtermuisknop op klikken en vervolgens **Explore Events** selecteren.

   [![Gebeurtenissen verkennen](media/quickstart/quickstart-explore-events.png)](media/quickstart/quickstart-explore-events.png#lightbox)

   Een raster met uw ruwe gegevens wordt weergegeven vanuit de regio die u verkent.

   [![Gebeurtenissen verkennen - rastergegevensweergave](media/quickstart/quickstart-explore-events-grid-data.png)](media/quickstart/quickstart-explore-events-grid-data.png#lightbox)

## <a name="select-and-filter-data"></a>Gegevens selecteren en filteren

1. Bewerk uw voorwaarden om de waarden in de grafiek te wijzigen. Voeg een andere term toe om verschillende typen waarden te correleren.

   [![Een term toevoegen](media/quickstart/quickstart-add-a-term.png)](media/quickstart/quickstart-add-a-term.png#lightbox)

1. Laat het vak **Filterreeks** leeg om alle geselecteerde zoektermen weer te geven of voer een filterterm in het vak **Filterreeks** voor geïmproviseerde seriefiltering.

   [![Reeks filteren](media/quickstart/quickstart-filter-series.png)](media/quickstart/quickstart-filter-series.png#lightbox)

   Voor deze quickstart voeren we **Station5** in om de temperatuur- en drukwaarden voor dat station kruislings te correleren.

Als u klaar bent met de quickstart, kunt u experimenteren met de set voorbeeldgegevens om verschillende visualisaties te maken.

## <a name="clean-up-resources"></a>Resources opschonen

Nu u de zelfstudie hebt voltooid, ruimt u de resources op die u hebt gemaakt:

1. Zoek in het linkermenu in de [Azure-portal](https://portal.azure.com) **Alle bronnen**, zoek uw Azure Time Series Insights-brongroep.
1. Verwijder de volledige resourcegroep (en alle bronnen daarin) door Elke resource afzonderlijk **te** verwijderen of te verwijderen.

## <a name="next-steps"></a>Volgende stappen

Je bent klaar om je eigen Time Series Insights-omgeving te maken:
> [!div class="nextstepaction"]
> [Uw Time Series Insights-omgeving plannen](time-series-insights-environment-planning.md)
