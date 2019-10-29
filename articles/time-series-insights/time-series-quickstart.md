---
title: 'Snelstartgids: Azure Time Series Insights Explorer | Microsoft Docs'
description: In deze Snelstartgids ziet u hoe u aan de slag kunt gaan met de Azure Time Series Insights Explorer in uw webbrowser om grote hoeveel heden IoT-gegevens te visualiseren. In dit artikel worden de belangrijkste functies van de verkenner van Azure Time Series Insights behandeld via een rondleiding in een demo-omgeving.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 09/23/2019
ms.openlocfilehash: 156e95c88bc3c66890ba9c8baf9b281330b60186
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990910"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Quickstart: Azure Time Series Insights verkennen

Met de Snelstartgids van Azure Time Series Insights Explorer kunt u aan de slag met Time Series Insights in een gratis demonstratie omgeving. In deze Quick Start leert u hoe u uw webbrowser kunt gebruiken om grote hoeveel heden IoT-gegevens en-Tour functies te visualiseren die nu algemeen beschikbaar zijn.

Azure Time Series Insights is een volledig beheerde analyse-, opslag-en visualisatie service die het eenvoudiger maakt om miljarden IoT-gebeurtenissen tegelijkertijd te verkennen en te analyseren. Het biedt u een wereld wijde weer gave van uw gegevens, zodat u uw IoT-oplossing snel kunt valideren en kost bare downtime van essentiële apparaten te voor komen. Azure Time Series Insights helpt u bij het ontdekken van verborgen trends, afwijkings afwijkingen en het uitvoeren van analyses in de buurt van de hoofd oorzaak.

Voor extra flexibiliteit kunt u Azure Time Series Insights toevoegen aan een bestaande toepassing via de krachtige [rest api's](./time-series-insights-update-tsq.md) en de [client-SDK](https://github.com/microsoft/tsiclient). U kunt de Api's gebruiken voor het opslaan, opvragen en gebruiken van Time Series-gegevens in een client toepassing naar keuze. U kunt ook de client-SDK gebruiken om UI-onderdelen toe te voegen aan uw bestaande toepassing.

Deze Quick Start van Time Series Insights Explorer biedt een rond leiding door de functies die algemeen beschikbaar zijn.

> [!IMPORTANT]
> Maak een [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) als u er nog geen hebt gemaakt.

## <a name="prepare-the-demo-environment"></a>De demo omgeving voorbereiden

1. Ga in uw browser naar de [demo over algemene Beschik baarheid](https://insights.timeseries.azure.com/demo).

1. Meld u bij de Time Series Insights Explorer aan met de referenties van uw Azure-account als u hierom wordt gevraagd.

1. De pagina met Time Series Insights snelle rond leiding wordt weer gegeven. Selecteer **volgende** om de rond leiding te starten.

   [![volgende selecteren](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>De demo omgeving verkennen

1. Het **deel venster tijd selecteren** wordt weer gegeven. Gebruik dit venster om het tijdsbestek te selecteren dat u wilt visualiseren.

   [deel venster![tijd selecteren](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Selecteer een tijds bestek en sleep dit in de regio. Selecteer vervolgens **zoeken**.

   [een tijds bestek![selecteren](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   U ziet in Time Series Insights een grafiek met een visualisatie voor het opgegeven tijdsbestek. U kunt verschillende acties uitvoeren in het lijn diagram. U kunt bijvoorbeeld filteren, vastmaken, sorteren en stapelen.

   Als u wilt terugkeren naar het **deel venster tijd**selecteren, selecteert u de pijl-omlaag zoals weer gegeven:

   [Grafiek![](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Selecteer **toevoegen** in het **deel venster voor waarden** om een nieuwe zoek term toe te voegen.

   [item![toevoegen](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. In de grafiek kunt u een regio selecteren, hier met de rechtermuisknop op klikken en vervolgens **Explore Events** selecteren.

   [Gebeurtenissen![verkennen](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   Een raster van uw onbewerkte gegevens wordt weer gegeven in de regio die u wilt verkennen.

   [Raster weergave![](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Gegevens selecteren en filteren

1. Bewerk uw voor waarden om de waarden in de grafiek te wijzigen. Voeg een andere term toe om verschillende soorten waarden kruislings te correleren.

   [![een term toevoegen](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Geef een filter term op in het vak **filter reeksen** voor filteren op Improvised-serie. Voor deze quickstart voeren we **Station5** in om de temperatuur- en drukwaarden voor dat station kruislings te correleren.

   [![-filter reeksen](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

Als u klaar bent met de quickstart, kunt u experimenteren met de set voorbeeldgegevens om verschillende visualisaties te maken.

## <a name="clean-up-resources"></a>Resources opschonen

Nu u de zelf studie hebt voltooid, kunt u de resources die u hebt gemaakt opschonen:

1. Selecteer in het menu links in het [Azure Portal](https://portal.azure.com) **alle resources**, zoek de Azure time series Insights resource groep.
1. Verwijder de hele resource groep (en alle resources erin) door elke resource afzonderlijk **verwijderen** of verwijderen te selecteren.

## <a name="next-steps"></a>Volgende stappen

U bent klaar om uw eigen Time Series Insights-omgeving te maken:
> [!div class="nextstepaction"]
> [Uw Time Series Insights-omgeving plannen](time-series-insights-environment-planning.md)
