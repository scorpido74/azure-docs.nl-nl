---
title: Verbind uw omgeving met Power BI-Azure Time Series Insights | Microsoft Docs
description: Informatie over het verbinden van Azure Time Series Insights naar Power BI voor het delen, het diagram en weer geven van gegevens in uw organisatie.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: e218877ee0d29e493a31091ccbce406ed888f8cc
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74114732"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Gegevens visualiseren van Time Series Insights in Power BI

Azure Time Series Insights is een platform voor het opslaan, beheren, opvragen en visualiseren van Time Series-gegevens in de Cloud. [Power bi](https://powerbi.microsoft.com) is een Business Analytics-hulp programma met uitgebreide visualisatie mogelijkheden waarmee u inzichten en resultaten kunt delen binnen uw organisatie. Beide services kunnen nu worden geïntegreerd om optimaal gebruik te maken van de Time Series Insights ' inherente visualisatie mogelijkheden en Power BI.

U leert het volgende:

* Time Series Insights verbinding maken met Power BI met behulp van de Cloud connector
* Visuals maken met uw gegevens in Power BI
* Het rapport publiceren naar Power BI en delen met de rest van uw organisatie

Aan het einde leert u hoe u gegevens in de tijd reeks kunt visualiseren met Azure Time Series Insights en deze kunt uitbreiden met de krachtige gegevens visualisatie en eenvoudig delen van Power BI.

Zorg ervoor dat u zich aanmeldt voor een [gratis Azure-abonnement](https://azure.microsoft.com/free/) als u er nog geen hebt.

## <a name="prerequisites"></a>Vereisten

* Down load en installeer de nieuwste versie van [Power bi Desktop](https://powerbi.microsoft.com/downloads/)
* Een [Azure time series INSIGHTS ga-exemplaar](time-series-insights-get-started.md) of [Azure time series Insights preview-exemplaar](time-series-insights-update-how-to-manage.md) maken

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Gegevens verbinden van Time Series Insights naar Power BI

Voer de volgende stappen uit om uw Time Series Insights-omgeving te verbinden met Power BI:

1. Time Series Insights Explorer openen                      
1. Gegevens exporteren als een query of als onbewerkte gegevens                       
1. Power BI Desktop openen
1. Laden vanuit aangepaste query

### <a name="export-data-into-power-bi-desktop"></a>Gegevens exporteren naar Power BI bureau blad

Aan de slag:

1. Open de Time Series Insights preview Explorer en uw gegevens te openen.
1. Wanneer u een weer gave hebt gemaakt waarin u tevreden bent, gaat u naar het vervolg keuzemenu **meer acties** en klikt u op **verbinding maken met Power bi**. 

    [![Time Series Insights preview Verkenner exporteren](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Stel de para meters in op dit tabblad:

   1. Geef een relatieve periode op die u wilt weer geven. Als u tevreden bent met uw bestaande weer gave, kunt u deze als een **bestaande periode**laten staan. 
   1. U kunt kiezen tussen **geaggregeerde** en **onbewerkte gebeurtenissen**. 
   
       > [!NOTE]
       > U kunt uw gegevens later in Power BI samen voegen, maar niet herstellen naar onbewerkte gegevens na aggregatie. 
       
       > [!NOTE]
       > Er is een limiet van 100 kB voor het aantal gebeurtenissen voor onbewerkte gebeurtenis niveau.

       [![verbinding maken](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Als u uw Time Series Insights-exemplaar voor warme Store niet hebt geconfigureerd, wordt een waarschuwing weer gegeven.

       [![verbinding maken](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > U kunt uw bestaande exemplaar configureren voor warme Store in de Azure Portal.

1. Selecteer **query kopiëren naar klem bord**.
1. Start nu Power BI Desktop.
1. Selecteer in Power BI Desktop op het tabblad **Start** de optie **gegevens ophalen** in de **linkerbovenhoek.**

    [vervolg keuzelijst ![Home](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Zoek naar **Time Series Insights**, selecteer **Azure time series Insights (bèta)** en vervolgens **verbinding maken**.

    [![verbinding maken met Power BI om Time Series Insights](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    U kunt ook naar het tabblad **Azure** gaan, **Azure time series Insights (bèta)** selecteren en vervolgens **verbinding maken**.
    
1. In een dialoog venster voor bericht wordt u gevraagd om toestemming te geven om verbinding te maken met bronnen van derden. Selecteer **door gaan**.

    [![Kies aangepaste query maken](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. Kies **aangepaste query maken**in het vervolg keuzemenu onder **gegevens bron**. Plak vanuit het klem bord in het onderstaande veld optionele **aangepaste query (optioneel)** en druk vervolgens op **OK**.

    [![Geef de aangepaste query door en selecteer OK.](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. De gegevens tabel wordt nu geladen. Druk op **laden** om in Power bi te laden.

    [![de geladen gegevens in de tabel controleren en laden selecteren](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Als u deze stappen hebt voltooid, gaat u verder met de volgende sectie.

## <a name="create-a-report-with-visuals"></a>Een rapport maken met visuele elementen

Nu u de gegevens in Power BI hebt geïmporteerd, is het tijd om een rapport met visuals te maken.

1. Zorg ervoor dat op de linkerkant van het venster de **rapport** weergave is geselecteerd.

    [de rapport weergave ![selecteren](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  Selecteer in de kolom **Visualisaties** uw visuele element van uw keuze. Selecteer bijvoorbeeld **lijn diagram**. Hiermee wordt een leeg lijn diagram aan uw canvas toegevoegd.
 
1.  Selecteer in de lijst **velden** de optie **tijds tempel** en sleep deze naar het **asveld** om items weer te geven langs de X-as.

1.  Klik opnieuw in de lijst **velden** op **TimeSeriesId** en sleep deze naar het veld **waarden** om items weer te geven op de Y-as.

    [een lijn diagram ![maken](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Als u nog een grafiek aan uw canvas wilt toevoegen, klikt u ergens op het canvas buiten het lijn diagram en herhaalt u dit proces.

    [![extra grafieken maken om te delen](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Zodra u het rapport hebt gemaakt, kunt u het publiceren naar Power BI Reporting Services.

## <a name="advanced-editing"></a>Geavanceerd bewerken

Als u al een gegevensset in Power BI hebt geladen, maar u de query wilt wijzigen (zoals de para meters voor datum/tijd of omgevings-ID), kunt u dit doen via de Geavanceerde editor functionaliteit van Power BI. Raadpleeg de [documentatie van Power bi](https://docs.microsoft.com/power-bi/desktop-query-overview) voor meer informatie.

Als een overzicht:

1. In Power BI Desktop selecteert u **Query's bewerken**.
1. Druk op **Geavanceerde editor**.

    [query's in de Geavanceerde editor ![bewerken](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Wijzig de JSON-Payload naar wens.
1. Selecteer **gereed** en **sluit & pas** in het venster van de **Power query editor**toe.

U ziet dat de gewenste wijzigingen worden toegepast.  

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Power bi connector concepten](https://docs.microsoft.com/power-bi/desktop-query-overview) voor Azure time series Insights.

* Meer informatie over [Power bi bureau blad](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Zie [Time Series INSIGHTS ga Verkenner](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) en [Time Series Insights preview Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).
