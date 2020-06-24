---
title: Aan de slag met de Azure Metrics Explorer
description: Meer informatie over het maken van uw eerste metrieke grafiek met Azure Metrics Explorer.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 920ff19b162843d1c2d631ad192ce1b3c4273a68
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84696980"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Aan de slag met Azure Metrics Explorer

## <a name="where-do-i-start"></a>Waar moet ik beginnen?
Azure Monitor Metrics Explorer is een onderdeel van de Microsoft Azure-portal waarmee grafieken kunnen worden getekend, trends visueel worden gecorreleerd en pieken en spannings dips in metrische waarden worden onderzocht. Gebruik metrische gegevens Verkenner om de status en het gebruik van uw resources te onderzoeken. Begin in de volgende volg orde:

1. [Kies een resource en metrische gegevens](#create-your-first-metric-chart) en u ziet een eenvoudige grafiek. [Selecteer vervolgens een tijds bereik](#select-a-time-range) dat relevant is voor uw onderzoek.

1. Probeer [dimensie filters toe te passen en te splitsen](#apply-dimension-filters-and-splitting). Met filters en splitsen kunt u analyseren welke segmenten van de metrische gegevens bijdragen aan de algemene metrische waarde en mogelijke uitschieters identificeren.

1. Gebruik [Geavanceerde instellingen](#advanced-chart-settings) om de grafiek aan te passen voordat u aan dash boards vastmaakt. [Waarschuwingen configureren](alerts-metric-overview.md) voor het ontvangen van meldingen wanneer de waarde van de metriek groter of kleiner dan een drempel overschrijdt.

## <a name="create-your-first-metric-chart"></a>Uw eerste metrieke grafiek maken

Als u een metrische grafiek wilt maken, vanuit uw resource, resource groep, abonnement of Azure Monitor weer gave, opent u het tabblad **metrische gegevens** en volgt u deze stappen:

1. Selecteer de resource waarvoor u de metrische gegevens wilt weer geven met behulp van de resource kiezer. (De resource wordt vooraf geselecteerd als u **metrische gegevens** in de context van een specifieke resource hebt geopend).

    > ![Een resource selecteren](./media/metrics-getting-started/resource-picker.png)

2. Voor sommige resources moet u een naam ruimte kiezen. De naam ruimte is een manier om metrische gegevens te organiseren, zodat u ze gemakkelijk kunt vinden. Zo hebben opslag accounts afzonderlijke naam ruimten voor het opslaan van bestanden, tabellen, blobs en wacht rijen. Veel resource typen hebben slechts één naam ruimte.

3. Selecteer een metrische waarde in een lijst met beschik bare metrische gegevens.

    > ![Een metriek selecteren](./media/metrics-getting-started/metric-picker.png)

4. U kunt desgewenst de metrische aggregatie wijzigen. U kunt bijvoorbeeld aangeven dat de grafiek minimum-, maximum-of gemiddelde waarden van de metriek moet weer geven.

> [!NOTE]
> Gebruik de knop **metriek toevoegen** en herhaal deze stappen als u meerdere metrische gegevens wilt zien die in dezelfde grafiek zijn getekend. Voor meerdere grafieken in één weer gave selecteert u de knop **grafiek toevoegen** bovenaan.

## <a name="select-a-time-range"></a>Een tijds bereik selecteren

In de grafiek worden standaard de meest recente 24 uur aan metrische gegevens weer gegeven. Gebruik het deel venster **tijd kiezer** om het tijds bereik te wijzigen, in te zoomen of uit te zoomen op de grafiek. 

![Deel venster tijds bereik wijzigen](./media/metrics-getting-started/time-picker.png)

> [!NOTE]
> Gebruik het **tijd penseel** om een interessant gebied van de grafiek (Prikker of een DIP) te onderzoeken. Plaats de muis aanwijzer aan het begin van het gebied, klik op de linkermuisknop en houd de muis knop ingedrukt, sleep naar de andere kant van het gebied en laat de knop los. De grafiek wordt in dat tijds bereik ingezoomd. 

## <a name="apply-dimension-filters-and-splitting"></a>Dimensie filters toep assen en splitsen

[Filteren](metrics-charts.md#apply-filters-to-charts) en [splitsen](metrics-charts.md#apply-splitting-to-a-chart) zijn krachtige diagnostische hulpprogram ma's voor metrische gegevens die dimensies hebben. Deze functies laten zien hoe diverse metrische gegevens segmenten ("dimensie waarden") van invloed zijn op de algemene waarde van de metrische gegevens en waarmee u mogelijke uitbijtingen kunt identificeren.

- Met **filters** kunt u kiezen welke dimensie waarden in de grafiek worden opgenomen. U kunt bijvoorbeeld geslaagde aanvragen weer geven bij het grafieken van de waarde voor de *reactie tijd* van de server. U moet het filter Toep assen op het *succes van de aanvraag* dimensie. 

- **Splitsen** bepaalt of het diagram afzonderlijke regels voor elke waarde van een dimensie weergeeft of de waarden in één regel samenvoegt. U kunt bijvoorbeeld één regel weer geven voor een gemiddelde reactie tijd voor alle Server exemplaren, of afzonderlijke regels voor elke server weer geven. U moet splitsen Toep assen op de dimensie van het *Server exemplaar* om afzonderlijke regels te bekijken.

Bekijk [voor beelden van de grafieken](metric-chart-samples.md) waarop filters en splitsingen zijn toegepast. In het artikel worden de stappen beschreven die zijn gebruikt om de grafieken te configureren.

## <a name="advanced-chart-settings"></a>Geavanceerde instellingen voor grafieken

U kunt de instellingen voor grafiek stijl, titel en geavanceerde grafiek aanpassen. Wanneer u klaar bent met aanpassing, kunt u deze vastmaken aan een dash board om uw werk op te slaan. U kunt ook waarschuwingen voor metrische gegevens configureren. Volg de [product documentatie](metrics-charts.md) voor meer informatie over deze en andere geavanceerde functies van Azure monitor Metrics Explorer.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over geavanceerde functies van Metrics Explorer](metrics-charts.md)
* [Problemen met Metrics Explorer oplossen](metrics-troubleshoot.md)
* [Een lijst met beschikbare metrische gegevens voor Azure-services zien](metrics-supported.md)
* [Voorbeelden van geconfigureerde grafieken zien](metric-chart-samples.md)
