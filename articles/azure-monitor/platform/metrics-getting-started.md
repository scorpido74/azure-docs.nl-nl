---
title: Aan de slag met Azure metrics explorer
description: Meer informatie over het maken van uw eerste metrische grafiek met Azure metrics explorer.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 920ff19b162843d1c2d631ad192ce1b3c4273a68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248773"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Aan de slag met Azure Metrics Explorer

## <a name="where-do-i-start"></a>Waar moet ik beginnen
Azure Monitor metrics explorer is een onderdeel van de Microsoft Azure-portal waarmee grafieken kunnen worden uitgestippeld, trends visueel kunnen correleren en pieken en dalen in de waarden van metrische gegevens kunnen worden onderzocht. Gebruik de statistiekenverkenner om de status en het gebruik van uw resources te onderzoeken. Start in de volgende volgorde:

1. [Kies een resource en een statistiek](#create-your-first-metric-chart) en u ziet een basisgrafiek. [Selecteer vervolgens een tijdsbereik](#select-a-time-range) dat relevant is voor uw onderzoek.

1. Probeer [dimensiefilters toe te passen en splitsen](#apply-dimension-filters-and-splitting). Met de filters en splitsing u analyseren welke segmenten van de statistiek bijdragen aan de totale metrische waarde en mogelijke uitschieters identificeren.

1. Gebruik [geavanceerde instellingen](#advanced-chart-settings) om de grafiek aan te passen voordat u aan dashboards vastmaakt. [Configureer waarschuwingen](alerts-metric-overview.md) om meldingen te ontvangen wanneer de metrische waarde een drempelwaarde overschrijdt of onderlaat.

## <a name="create-your-first-metric-chart"></a>Uw eerste metrische grafiek maken

Als u een metrische grafiek wilt maken vanuit uw resource, resourcegroep, abonnement of Azure Monitor-weergave, opent u het tabblad **Metrische gegevens** en volgt u de volgende stappen:

1. Selecteer met de resourcekiezer de resource waarvoor u statistieken wilt zien. (De resource is vooraf geselecteerd als u **Metrische gegevens** hebt geopend in de context van een specifieke resource).

    > ![Een resource selecteren](./media/metrics-getting-started/resource-picker.png)

2. Voor sommige bronnen moet u een naamruimte kiezen. De naamruimte is slechts een manier om statistieken te organiseren, zodat u ze gemakkelijk vinden. Opslagaccounts hebben bijvoorbeeld afzonderlijke naamruimten voor het opslaan van statistieken voor bestanden, tabellen, blobs en wachtrijen. Veel resourcetypen hebben slechts één naamruimte.

3. Selecteer een statistiek uit een lijst met beschikbare statistieken.

    > ![Een statistiek selecteren](./media/metrics-getting-started/metric-picker.png)

4. Optioneel u de metrische aggregatie wijzigen. U wilt bijvoorbeeld dat uw grafiek minimum-, maximum- of gemiddelde waarden van de statistiek weergeeft.

> [!NOTE]
> Gebruik de knop **Metrische gegevens toevoegen** en herhaal deze stappen als u meerdere statistieken in dezelfde grafiek wilt zien. Selecteer de knop **Grafiek toevoegen** bovenaan voor meerdere grafieken in één weergave.

## <a name="select-a-time-range"></a>Een tijdsbereik selecteren

Standaard toont de grafiek de meest recente 24 uur aan metrische gegevens. Gebruik het **deelvenster Tijdkiezer** om het tijdbereik te wijzigen, in te zoomen of uit te zoomen op de grafiek. 

![Deelvenster Tijdbereik wijzigen](./media/metrics-getting-started/time-picker.png)

> [!NOTE]
> Gebruik de **tijdborstel** om een interessant gebied van de grafiek te onderzoeken (spike of een dip). Zet de muisaanwijzer aan het begin van het gebied, klik en houd de linker muisknop ingedrukt, sleep naar de andere kant van het gebied en laat de knop los. De grafiek zoomt in op dat tijdsbereik. 

## <a name="apply-dimension-filters-and-splitting"></a>Dimensiefilters en splitsen toepassen

[Filteren](metrics-charts.md#apply-filters-to-charts) en [splitsen](metrics-charts.md#apply-splitting-to-a-chart) zijn krachtige diagnostische tools voor de statistieken die dimensies hebben. Deze functies laten zien hoe verschillende metrische segmenten ('dimensiewaarden') de totale waarde van de statistiek beïnvloeden en stellen u in staat mogelijke uitschieters te identificeren.

- **Met filteren** u kiezen welke dimensiewaarden in de grafiek zijn opgenomen. U bijvoorbeeld succesvolle aanvragen weergeven bij het in kaart brengen van de *serverresponstijdstatistiek.* U moet het filter toepassen op het succes van de *aanvraagdimensie.* 

- **Hiermee wordt** besturingselementen gesplitst of in de grafiek afzonderlijke regels voor elke waarde van een dimensie worden weergegeven of worden de waarden samengevoegd tot één regel. U bijvoorbeeld één regel zien voor een gemiddelde responstijd voor alle serverinstanties of afzonderlijke regels voor elke server bekijken. U moet splitsen toepassen op de dimensie van de *serverinstantie* om afzonderlijke regels te zien.

Zie [voorbeelden van de grafieken](metric-chart-samples.md) waarop filtering en splitsing zijn toegepast. Het artikel toont de stappen die zijn gebruikt om de grafieken te configureren.

## <a name="advanced-chart-settings"></a>Geavanceerde grafiekinstellingen

U grafiekstijl, titel en geavanceerde grafiekinstellingen aanpassen. Als u klaar bent met aanpassen, maakt u deze vast aan een dashboard om uw werk op te slaan. U ook metrische waarschuwingen configureren. Volg [productdocumentatie](metrics-charts.md) voor meer informatie over deze en andere geavanceerde functies van Azure Monitor metrics explorer.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over geavanceerde functies van Metrics Explorer](metrics-charts.md)
* [Problemen met Metrics Explorer oplossen](metrics-troubleshoot.md)
* [Een lijst met beschikbare metrische gegevens voor Azure-services zien](metrics-supported.md)
* [Voorbeelden van geconfigureerde grafieken zien](metric-chart-samples.md)
