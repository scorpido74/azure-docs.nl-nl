---
title: Uw omgeving verbinden met Inzichten in Power BI - Azure Time Series | Microsoft Documenten
description: Meer informatie over het verbinden van Inzichten in Azure Time Series met Power BI om gegevens in uw hele organisatie te delen, in kaart te brengen en weer te geven.
author: deepakpalled
ms.author: dpalled
manager: cshankar
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 22053bdc3a9836b76aa92303234a095cac6448ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863839"
---
# <a name="visualize-data-from-time-series-insights-in-power-bi"></a>Visualiseer gegevens uit inzichten uit de Time Series in Power BI

Azure Time Series Insights is een platform voor het opslaan, beheren, bevragen en visualiseren van tijdreeksgegevens in de cloud. [Power BI](https://powerbi.microsoft.com) is een business analytics tool met uitgebreide visualisatiemogelijkheden waarmee u inzichten en resultaten in uw hele organisatie delen. Beide services kunnen nu worden geïntegreerd om het beste uit de inherente visualisatiemogelijkheden van Time Series Insights te halen, evenals die van Power BI.

U leert het volgende:

* Connect Time Series Insights to Power BI met de cloudconnector
* Visuals maken met uw gegevens in Power BI
* Het rapport publiceren naar Power BI en delen met de rest van uw organisatie

Tegen het einde leert u hoe u tijdreeksgegevens visualiseren via Azure Time Series Insights en deze verbeteren met de sterke gegevensvisualisatie en eenvoudige mogelijkheden voor het delen van Power BI.

Zorg ervoor dat u zich aanmeldt voor een [gratis Azure-abonnement](https://azure.microsoft.com/free/) als u er nog geen hebt.

## <a name="prerequisites"></a>Vereisten

* De nieuwste versie van [Power BI Desktop](https://powerbi.microsoft.com/downloads/) downloaden en installeren
* Een voorbeeld van [Azure Time Series Insights Preview](time-series-insights-update-how-to-manage.md) hebben of maken

> [!IMPORTANT]
> De Power BI-connector wordt momenteel ondersteund in Time Series Insights *Preview-pay-as-you-go-omgevingen* die zijn geconfigureerd voor **Warm Store.**

## <a name="connect-data-from-time-series-insights-to-power-bi"></a>Gegevens uit inzichten uit de time-serie verbinden met Power BI

Voer de volgende stappen uit om uw Time Series Insights-omgeving aan te sluiten op Power BI:

1. Inzichten Explorer voor open tijdreeksen
1. Gegevens exporteren als query of als ruwe gegevens
1. Power BI-bureaublad openen
1. Laden vanuit aangepaste query

### <a name="export-data-into-power-bi-desktop"></a>Gegevens exporteren naar Power BI-bureaublad

Aan de slag:

1. Open de Time Series Insights Preview Explorer en beheer uw gegevens.
1. Zodra u een weergave hebt gemaakt waarmee u tevreden bent, navigeert u naar het vervolgkeuzemenu **Meer acties** en selecteert u Verbinding maken met **Power BI**.

    [![Export van Time Series Insights Preview Explorer](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. Stel uw parameters in op dit tabblad:

   1. Geef een relatief tijdsbestek op dat moet worden weergegeven. Als u tevreden bent met uw bestaande weergave, laat dit dan als **bestaand tijdsbestek**.
   
   1. Kies tussen **geaggregeerde** en **ruwe gebeurtenissen**. 
   
       > [!NOTE]
       > U uw gegevens later in Power BI altijd samenvoegen, maar u na aggregatie niet terugkeren naar ruwe gegevens. 
       
       > [!NOTE]
       > Er is een limiet voor het aantal gebeurtenissen van 100.000 voor gegevens op Raw-gebeurtenisniveau.

       [![Verbinding maken](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. Als u uw Time Series Insights-exemplaar voor **Warm Store**niet hebt geconfigureerd, ontvangt u een waarschuwing.

       [![Waarschuwing warme winkel](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > U uw bestaande instantie configureren voor **Warm Store** in de Azure-portal.

1. Selecteer **Query kopiëren naar klembord**.
1. Start nu Power BI Desktop.
1. Selecteer in Power BI-bureaublad op het tabblad **Start** de optie Gegevens in de linkerbovenhoek **opvragen** en vervolgens **Meer**.

    [![Home dropdown](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. Zoek naar **Time Series Insights,** selecteer **Azure Time Series Insights (Beta)** en **maak vervolgens verbinding**.

    [![Power BI verbinden met inzichten uit de tijdreeks](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    U ook naar het **tabblad Azure** navigeren, azure **timeseries insights (bèta)** selecteren en **vervolgens Verbinding maken**.
    
1. In een dialoogvenster voor berichtdialogen wordt toestemming gevraagd om verbinding te maken met bronnen van derden. Selecteer **Doorgaan**.

    [![Aangepaste query maken kiezen](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. Kies in het vervolgkeuzemenu onder **Gegevensbron**de optie **Aangepaste query maken**. Plak van het klembord in het optionele veld **Aangepaste query (optioneel)** hieronder en druk op **OK**.

    [![Geef de aangepaste query door en selecteer OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. De gegevenstabel wordt nu geladen. Druk **op Laden** om in Power BI te laden.

    [![De geladen gegevens in de tabel controleren en Laden selecteren](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

Als u deze stappen hebt voltooid, gaat u verder naar de volgende sectie.

## <a name="create-a-report-with-visuals"></a>Een rapport maken met visuele elementen

Nu u de gegevens hebt geïmporteerd in Power BI, is het tijd om een rapport met visuals te maken.

1. Controleer aan de linkerkant van het venster of u de **rapportweergave** hebt geselecteerd.

    [![De rapportweergave selecteren](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1.  Selecteer in de kolom **Visualisaties** de visuele weergave van keuze. Selecteer bijvoorbeeld **Lijndiagram**. Hiermee wordt een leeg lijndiagram aan uw canvas toegevoegd.
 
1.  Selecteer **in** de lijst Velden **Timestamp** en sleep deze naar het veld **As** om items langs de X-as weer te geven.

1.  Selecteer nogmaals in de lijst **Velden** **TimeSeriesId** en sleep deze naar het veld **Waarden** om items langs de Y-as weer te geven.

    [![Een lijndiagram maken](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1.  Als u een andere grafiek aan uw canvas wilt toevoegen, selecteert u ergens op het canvas buiten het lijndiagram en herhaalt u dit proces.

    [![Extra grafieken maken om te delen](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Zodra u uw rapport hebt gemaakt, u het publiceren naar Power BI Reporting Services.

## <a name="advanced-editing"></a>Geavanceerde bewerking

Als u al een gegevensset in Power BI hebt geladen, maar de query wilt wijzigen (zoals de parameters datum/tijd of omgevings-ID), u dit doen via de Geavanceerde editor-functionaliteit van Power BI. Raadpleeg de [Power BI-documentatie](https://docs.microsoft.com/power-bi/desktop-query-overview) voor meer informatie.

Als overzicht:

1. Selecteer in Power BI-bureaublad **query's bewerken**.
1. Druk op **Geavanceerde editor**.

    [![Query's bewerken in de geavanceerde editor](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. Wijzig de JSON payload naar wens.
1. Selecteer **Gereed** en **sluit & toepassen** in het Venster Power **Query-editor**.

De interface geeft nu de gewenste wijzigingen weer die u hebt toegepast.  

## <a name="next-steps"></a>Volgende stappen

* Lees meer over [Power BI-connectorconcepten](https://docs.microsoft.com/power-bi/desktop-query-overview) voor Azure Time Series Insights.

* Meer informatie over [Power BI-bureaublad](https://docs.microsoft.com/power-bi/desktop-query-overview).

* Lees [Time Series Insights GA Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart) en Time Series Insights Preview [Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).
