---
title: Zelfstudie - Een metrische grafiek maken in Azure Monitor
description: Meer informatie over het maken van uw eerste metrische grafiek met Azure metrics explorer.
author: bwren
ms.author: bwren
ms.subservice: metrics
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: d8f72471dd3109bf584d18f032ec2f4d949a4993
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79082809"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>Zelfstudie: Een metrische grafiek maken in Azure Monitor
Metrics explorer is een functie van Azure Monitor in de Azure-portal waarmee u grafieken maken op basis van metrische waarden, trends visueel correleren en pieken en dips in metrische waarden onderzoeken. Gebruik de verkenner met statistieken om de status en het gebruik van uw Azure-bronnen te onderzoeken of om grafieken te plotten op maatstaven. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een statistiek selecteren waarvoor u een grafiek wilt plotten
> * Verschillende aggregaties van metrische waarden uitvoeren
> * Het tijdbereik en de granulariteit voor de grafiek wijzigen

Hieronder volgt een video die een uitgebreider scenario laat zien dan de procedure die in dit artikel wordt beschreven. Als je nieuw bent in statistieken, raden we je aan dit artikel eerst te lezen en vervolgens de video te bekijken om meer details te zien. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, hebt u een Azure-bron nodig om te controleren. U elke bron in uw Azure-abonnement gebruiken die statistieken ondersteunt. Als u wilt bepalen of een resource metrische gegevens ondersteunt, gaat u naar het menu in de Azure-portal en controleert u of er een **optie Metrische gegevens** is opgenomen in het gedeelte **Controle** van het menu.


## <a name="log-in-to-azure"></a>Meld u aan bij Azure.
Log in bij de [https://portal.azure.com](https://portal.azure.com)Azure-portal op .

## <a name="open-metrics-explorer-and-select-a-scope"></a>Hiermee u meteenverkenner openen en een bereik selecteren
U metrics explorer openen in het menu Azure Monitor of via het menu van een resource in de Azure-portal. Statistieken uit alle bronnen zijn beschikbaar, ongeacht welke optie u gebruikt. 

1. Selecteer **Statistieken** in het menu **Azure Monitor** of in de sectie **Controle** van het menu van een resource.

1. Selecteer het **bereik**, de bron waarvoor u statistieken wilt weergeven. Het bereik is al gevuld als u de verkenner van de statistieken hebt geopend vanuit het menu van een resource.

    ![Een bereik selecteren](media/tutorial-metrics-explorer/scope-picker.png)

2. Selecteer een **naamruimte** als het bereik meer dan één heeft. De naamruimte is slechts een manier om statistieken te organiseren, zodat u ze gemakkelijk vinden. Opslagaccounts hebben bijvoorbeeld afzonderlijke naamruimten voor het opslaan van statistieken voor bestanden, tabellen, blobs en wachtrijen. Veel resourcetypen hebben slechts één naamruimte.

3. Selecteer een statistiek uit een lijst met beschikbare statistieken voor het geselecteerde bereik en de naamruimte.

    ![Een statistiek selecteren](media/tutorial-metrics-explorer/metric-picker.png)

4. Wijzig eventueel de metrische **aggregatie**. Hiermee wordt gedefinieerd hoe de metrische waarden worden samengevoegd gedurende de tijdsgranulariteit voor de grafiek. Als de tijdgranulariteit bijvoorbeeld is ingesteld op 15 minuten en de aggregatie is ingesteld op som, dan is elk punt in de grafiek de som van alle verzamelde waarden over elk segment van 15 minuten.

    ![Grafiek](media/tutorial-metrics-explorer/chart.png)

5. Gebruik de knop **Metrische gegevens toevoegen** en herhaal deze stappen als u meerdere statistieken in dezelfde grafiek wilt zien. Selecteer de knop **Nieuwe grafiek** voor meerdere grafieken in één weergave.

## <a name="select-a-time-range-and-granularity"></a>Een tijdsbereik en granulariteit selecteren

Standaard toont de grafiek de meest recente 24 uur aan metrische gegevens. Gebruik de tijdkiezer om het **tijdsbereik** voor de grafiek of de **tijdgranulariteit** te wijzigen die het tijdsbereik voor elk gegevenspunt definieert. De grafiek gebruikt de opgegeven aggregatie om alle bemonsterde waarden te berekenen over de opgegeven tijdsgranulariteit.

![Deelvenster Tijdbereik wijzigen](media/tutorial-metrics-explorer/time-picker.png)


Gebruik de **tijdborstel** om een interessant gebied van de grafiek te onderzoeken, zoals een piek of een dip. Plaats de muisaanwijzer aan het begin van het gebied, klik op de linkermuisknop en houd deze ingedrukt, sleep naar de andere kant van het gebied en laat de knop los. De grafiek zoomt in op dat tijdsbereik. 

![Tijdpenseel](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>Dimensiefilters en splitsen toepassen
Bekijk de volgende verwijzingen naar geavanceerde functies waarmee u aanvullende analyses op uw statistieken uitvoeren en potentiële uitschieters in uw gegevens identificeren.

- [Met filteren](../platform/metrics-charts.md#apply-filters-to-charts) u kiezen welke dimensiewaarden in de grafiek zijn opgenomen. U bijvoorbeeld alleen succesvolle aanvragen weergeven wanneer u een *serverresponstijdstatistiek* in kaart brengt. 

- [Hiermee wordt](../platform/metrics-charts.md#apply-splitting-to-a-chart) besturingselementen gesplitst of in de grafiek afzonderlijke regels voor elke waarde van een dimensie worden weergegeven of worden de waarden samengevoegd tot één regel. U bijvoorbeeld één regel zien voor een gemiddelde responstijd voor alle serverinstanties of u wilt mogelijk afzonderlijke regels voor elke server. 

Zie [voorbeelden van de grafieken](../platform/metric-chart-samples.md) waarop filtering en splitsing zijn toegepast.

## <a name="advanced-chart-settings"></a>Geavanceerde grafiekinstellingen

U grafiekstijl, titel en geavanceerde grafiekinstellingen aanpassen. Als u klaar bent met aanpassen, maakt u deze vast aan een dashboard om uw werk op te slaan. U ook metrische waarschuwingen configureren. Zie [Geavanceerde functies van Azure Metrics Explorer](../platform/metrics-charts.md#lock-boundaries-of-chart-y-axis) voor meer informatie over deze en andere geavanceerde functies van Azure Monitor metrics explorer.


## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd hoe u met statistieken werken in Azure Monitor, leest u hoe u statistieken gebruiken om proactieve waarschuwingen te verzenden.

> [!div class="nextstepaction"]
> [Metrische waarschuwing maken, bekijken en beheren met Azure Monitor](../platform/alerts-metric.md)

