---
title: 'Zelf studie: een grafiek met metrische gegevens maken in Azure Monitor'
description: Meer informatie over het maken van uw eerste metrieke grafiek met Azure Metrics Explorer.
author: bwren
ms.author: bwren
ms.subservice: metrics
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: d8f72471dd3109bf584d18f032ec2f4d949a4993
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082809"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>Zelf studie: een grafiek met metrische gegevens maken in Azure Monitor
Metrics Explorer is een functie van Azure Monitor in de Azure Portal waarmee u grafieken kunt maken op basis van metrische waarden, trends visueel kunt correleren en pieken en spannings dips in metrische waarden kan onderzoeken. Gebruik metrische gegevens Verkenner om de status en het gebruik van uw Azure-resources te onderzoeken of om grafieken uit aangepaste metrische gegevens te tekenen. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een metriek selecteren waarvoor u een grafiek wilt uitzetten
> * Verschillende aggregaties van metrische waarden uitvoeren
> * Het tijds bereik en de granulariteit voor de grafiek wijzigen

Hieronder volgt een video waarin een uitgebreidere scenario wordt weer gegeven dan de procedure die wordt beschreven in dit artikel. Als u niet bekend bent met metrische gegevens, wordt u aangeraden dit artikel eerst te lezen en vervolgens de video te bekijken om meer details weer te geven. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze zelf studie hebt u een Azure-resource nodig om te bewaken. U kunt elke resource in uw Azure-abonnement gebruiken dat metrische gegevens ondersteunt. Als u wilt bepalen of een resource metrische gegevens ondersteunt, gaat u naar het menu in het Azure Portal en controleert u of er een **metrische** optie is in de sectie **bewaking** van het menu.


## <a name="log-in-to-azure"></a>Meld u aan bij Azure.
Meld u aan bij de Azure-portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="open-metrics-explorer-and-select-a-scope"></a>Metrische gegevens Verkenner openen en een bereik selecteren
U kunt metrische gegevens Verkenner openen vanuit het Azure Monitor menu of vanuit het menu van een resource in de Azure Portal. De metrische gegevens van alle resources zijn beschikbaar, ongeacht de optie die u gebruikt. 

1. Selecteer **metrische gegevens** in het menu **Azure monitor** of in de sectie **bewaking** van het menu van een resource.

1. Selecteer het **bereik**. Dit is de resource waarvoor u de metrische gegevens wilt weer geven. Het bereik is al ingevuld als u metrische gegevens Verkenner hebt geopend vanuit het menu van een resource.

    ![Een bereik selecteren](media/tutorial-metrics-explorer/scope-picker.png)

2. Selecteer een **naam ruimte** als het bereik meer dan één heeft. De naam ruimte is een manier om metrische gegevens te organiseren, zodat u ze gemakkelijk kunt vinden. Zo hebben opslag accounts afzonderlijke naam ruimten voor het opslaan van bestanden, tabellen, blobs en wacht rijen. Veel resource typen hebben slechts één naam ruimte.

3. Selecteer een metriek in een lijst met beschik bare metrische gegevens voor het geselecteerde bereik en de naam ruimte.

    ![Een metriek selecteren](media/tutorial-metrics-explorer/metric-picker.png)

4. Wijzig desgewenst de metrische **aggregatie**. Hiermee definieert u hoe de metrische waarden worden geaggregeerd over de tijd granulariteit voor de grafiek. Als de tijd granulatie bijvoorbeeld is ingesteld op 15 minuten en de aggregatie is ingesteld op Sum, wordt elk punt in de grafiek de som van alle verzamelde waarden voor elk segment van 15 minuten.

    ![Grafiek](media/tutorial-metrics-explorer/chart.png)

5. Gebruik de knop **metriek toevoegen** en herhaal deze stappen als u meerdere metrische gegevens wilt zien die in dezelfde grafiek zijn getekend. Selecteer de knop **nieuwe grafiek** voor meerdere grafieken in één weer gave.

## <a name="select-a-time-range-and-granularity"></a>Een tijds bereik en granulatie selecteren

In de grafiek worden standaard de meest recente 24 uur aan metrische gegevens weer gegeven. Gebruik de tijd kiezer om het **tijds bereik** voor de grafiek of de **tijd granulatie** te wijzigen waarmee het tijds bereik voor elk gegevens punt wordt gedefinieerd. In de grafiek wordt de opgegeven aggregatie gebruikt voor het berekenen van alle voorbeeld waarden ten opzichte van de opgegeven tijd granulatie.

![Deel venster tijds bereik wijzigen](media/tutorial-metrics-explorer/time-picker.png)


Gebruik het **tijd penseel** om een interessant gebied van de grafiek te onderzoeken, zoals een Prikker of een dip. Plaats de muis aanwijzer aan het begin van het gebied, klik op de linkermuisknop en houd de muis knop ingedrukt, sleep naar de andere kant van het gebied en laat de knop los. De grafiek wordt in dat tijds bereik ingezoomd. 

![Tijd kwast](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>Dimensie filters toep assen en splitsen
Raadpleeg de volgende Naslag informatie voor geavanceerde functies waarmee u extra analyses kunt uitvoeren op uw metrische gegevens en mogelijke uitschieters in uw data kan identificeren.

- Met [filters](../platform/metrics-charts.md#apply-filters-to-charts) kunt u kiezen welke dimensie waarden in de grafiek worden opgenomen. U kunt bijvoorbeeld alleen geslaagde aanvragen weer geven bij het diagram van de metrische gegevens van een *Server reactie tijd* . 

- [Splitsen](../platform/metrics-charts.md#apply-splitting-to-a-chart) bepaalt of het diagram afzonderlijke regels voor elke waarde van een dimensie weergeeft of de waarden in één regel samenvoegt. U kunt bijvoorbeeld één regel zien voor een gemiddelde reactie tijd voor alle Server exemplaren of u wilt afzonderlijke regels voor elke server. 

Bekijk [voor beelden van de grafieken](../platform/metric-chart-samples.md) waarop filters en splitsingen zijn toegepast.

## <a name="advanced-chart-settings"></a>Geavanceerde instellingen voor grafieken

U kunt de instellingen voor grafiek stijl, titel en geavanceerde grafiek aanpassen. Wanneer u klaar bent met aanpassing, kunt u deze vastmaken aan een dash board om uw werk op te slaan. U kunt ook waarschuwingen voor metrische gegevens configureren. Zie [geavanceerde functies van Azure Metrics Explorer](../platform/metrics-charts.md#lock-boundaries-of-chart-y-axis) voor meer informatie over deze en andere geavanceerde functies van Azure monitor Metrics Explorer.


## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd hoe u met metrische gegevens in Azure Monitor kunt werken, leert u hoe u metrische gegevens kunt gebruiken om proactieve waarschuwingen te verzenden.

> [!div class="nextstepaction"]
> [Metrische waarschuwing maken, bekijken en beheren met Azure Monitor](../platform/alerts-metric.md)

