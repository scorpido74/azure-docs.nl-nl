---
title: Exporteren met Behulp van Stream Analytics vanuit Azure Application Insights | Microsoft Documenten
description: Stream Analytics kan de gegevens die u exporteert vanuit Application Insights continu transformeren, filteren en routeren.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 15d1efa3a632024429d41f27fc23c569cd85bec2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536876"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Stream Analytics gebruiken om geëxporteerde gegevens te verwerken vanuit Application Insights
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) is de ideale tool voor het verwerken van gegevens [die worden geëxporteerd vanuit Application Insights.](export-telemetry.md) Stream Analytics kan gegevens uit verschillende bronnen halen. Het kan transformeren en filteren van de gegevens, en vervolgens route naar een verscheidenheid van putten.

In dit voorbeeld maken we een adapter die gegevens uit Application Insights gebruikt, een aantal velden hernoemt en verwerkt en deze naar Power BI brengt.

> [!WARNING]
> Er zijn veel betere en eenvoudigere [aanbevolen manieren om Application Insights-gegevens weer te geven in Power BI.](../../azure-monitor/app/export-power-bi.md ) Het hier geïllustreerde pad is slechts een voorbeeld om te illustreren hoe geëxporteerde gegevens moeten worden verwerkt.
> 
> 

![Blokdiagram voor export via SA naar PBI](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Opslag maken in Azure
Continue export levert altijd gegevens op naar een Azure Storage-account, dus u moet eerst de opslag maken.

1. Maak een 'klassiek' opslagaccount in uw abonnement in de [Azure-portal.](https://portal.azure.com)
   
   ![Kies in Azure-portal Nieuw, Gegevens, Opslag](./media/export-stream-analytics/030.png)
2. Een container maken
   
    ![Selecteer in de nieuwe opslag Containers, klik op de tegel Containers en voeg vervolgens Toe](./media/export-stream-analytics/040.png)
3. De opslagtoegangssleutel kopiëren
   
    Je hebt het snel nodig om de invoer voor de stream analytics-service in te stellen.
   
    ![Open in de opslag Instellingen, Toetsen en neem een kopie van de primaire toegangssleutel](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Continue export starten naar Azure-opslag
[Continue export](export-telemetry.md) verplaatst gegevens van Application Insights naar Azure-opslag.

1. Blader in de Azure-portal naar de Application Insights-bron die u voor uw toepassing hebt gemaakt.
   
    ![Kies Bladeren, toepassingsinzichten, uw toepassing](./media/export-stream-analytics/050.png)
2. Maak een continue export.
   
    ![Kies Instellingen, Continu exporteren, Toevoegen](./media/export-stream-analytics/060.png)

    Selecteer het opslagaccount dat u eerder hebt gemaakt:

    ![De exportbestemming instellen](./media/export-stream-analytics/070.png)

    Stel de gebeurtenistypen in die u wilt zien:

    ![Gebeurtenistypen kiezen](./media/export-stream-analytics/080.png)

1. Laat sommige gegevens zich ophopen. Achterover leunen en laat mensen gebruik maken van uw toepassing voor een tijdje. Telemetrie komt binnen en u ziet statistische grafieken in [metrische verkenner](../../azure-monitor/platform/metrics-charts.md) en afzonderlijke gebeurtenissen in [diagnostisch zoeken.](../../azure-monitor/app/diagnostic-search.md) 
   
    En ook zullen de gegevens exporteren naar uw opslag. 
2. Controleer de geëxporteerde gegevens. Kies in Visual Studio **Weergave / Cloud Explorer**en open Azure / Storage. (Als u deze menuoptie niet hebt, moet u de Azure SDK installeren: Open het dialoogvenster Nieuw project en open Visual C# / Cloud / Get Microsoft Azure SDK voor .NET.)
   
    ![](./media/export-stream-analytics/04-data.png)
   
    Noteer het algemene deel van de padnaam, dat is afgeleid van de toepassingsnaam en de instrumentatietoets. 

De gebeurtenissen worden geschreven naar blobbestanden in JSON-indeling. Elk bestand kan een of meer gebeurtenissen bevatten. Daarom willen we de gebeurtenisgegevens lezen en de velden filteren die we willen. Er zijn allerlei dingen die we kunnen doen met de gegevens, maar ons plan vandaag is om Stream Analytics te gebruiken om de gegevens door te geven aan Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Een Azure Stream Analytics-exemplaar maken
Selecteer in de [Azure-portal](https://portal.azure.com/)de Azure Stream Analytics-service en maak een nieuwe Stream Analytics-taak:

![](./media/export-stream-analytics/SA001.png)

![](./media/export-stream-analytics/SA002.png)

Wanneer de nieuwe taak is gemaakt, selecteert u **Ga naar resource**.

![](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Een nieuwe invoer toevoegen

![](./media/export-stream-analytics/SA004.png)

Stel deze in om invoer uit de blob Continue export te nemen:

![](./media/export-stream-analytics/SA0005.png)

Nu heb je de primaire toegangssleutel nodig van je opslagaccount, die je eerder hebt opgemerkt. Stel dit in als de opslagaccountsleutel.

### <a name="set-path-prefix-pattern"></a>Padvoorvoegingspatroon instellen

**Zorg ervoor dat u de datumnotatie instelt op YYYY-MM-DD (met streepjes).**

Het padvoorvoegingspatroon geeft aan waar Stream Analytics de invoerbestanden in de opslag vindt. U moet deze instellen om overeen te komen met de manier waarop Continuous Export de gegevens opslaat. Stel het als volgt in:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

In dit voorbeeld:

* `webapplication27`is de naam van de Application Insights-bron **in alle kleine letters.**
* `1234...`is de instrumentatiesleutel van de Application Insights-bron, **die streepjes weglaat.** 
* `PageViews`is het type gegevens dat u wilt analyseren. De beschikbare typen zijn afhankelijk van het filter dat u instelt in Continue export. Controleer de geëxporteerde gegevens om de andere beschikbare typen te bekijken en zie het [exportgegevensmodel](export-data-model.md).
* `/{date}/{time}`is een patroon letterlijk geschreven.

> [!NOTE]
> Inspecteer de opslag om er zeker van te zijn dat u het pad goed krijgt.
> 

## <a name="add-new-output"></a>Nieuwe uitvoer toevoegen
Selecteer nu uw taak > **Uitvoer** > **toevoegen**.

![](./media/export-stream-analytics/SA006.png)


![Selecteer het nieuwe kanaal, klik op Uitvoer, Toevoegen, Power BI](./media/export-stream-analytics/SA010.png)

Geef je **werk- of schoolaccount** op om Stream Analytics te autoriseren om toegang te krijgen tot je Power BI-bron. Bedenk vervolgens een naam voor de uitvoer en voor de doel-Power BI-gegevensset en -tabel.

## <a name="set-the-query"></a>De query instellen
De query regelt de vertaling van invoer naar uitvoer.

Gebruik de functie Test om te controleren of u de juiste uitvoer krijgt. Geef het de voorbeeldgegevens die u van de invoerpagina hebt genomen. 

### <a name="query-to-display-counts-of-events"></a>Query om het aantal gebeurtenissen weer te geven
Plak deze query:

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-input is de alias die we aan de stream-invoer hebben gegeven
* pbi-output is de uitvoeralias die we hebben gedefinieerd
* We gebruiken [OUTER APPLY GetElements](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) omdat de gebeurtenisnaam zich in een geneste JSON-array bevindt. Vervolgens kiest de selectie de naam van de gebeurtenis, samen met een telling van het aantal exemplaren met die naam in de periode. De [groep door](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) clausule groepeert de elementen in tijdsperiodes van één minuut.

### <a name="query-to-display-metric-values"></a>Query om metrische waarden weer te geven
```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* Deze query zoomt in op de telemetrie van de statistieken om de gebeurtenistijd en de metrische waarde te krijgen. De metrische waarden bevinden zich in een array, dus we gebruiken het patroon OUTER APPLY GetElements om de rijen te extraheren. "myMetric" is de naam van de metrische in dit geval. 

### <a name="query-to-include-values-of-dimension-properties"></a>Query om waarden van dimensieeigenschappen op te nemen
```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* Deze query bevat waarden van de dimensieeigenschappen zonder afhankelijk te zijn van een bepaalde dimensie die bij een vaste index in de dimensiearray zit.

## <a name="run-the-job"></a>De taak uitvoeren
U een datum in het verleden selecteren om de taak van te starten. 

![Selecteer de taak en klik op Query. Plak het onderstaande monster.](./media/export-stream-analytics/SA008.png)

Wacht tot de taak loopt.

## <a name="see-results-in-power-bi"></a>Bekijk de resultaten in Power BI
> [!WARNING]
> Er zijn veel betere en eenvoudigere [aanbevolen manieren om Application Insights-gegevens weer te geven in Power BI.](../../azure-monitor/app/export-power-bi.md ) Het hier geïllustreerde pad is slechts een voorbeeld om te illustreren hoe geëxporteerde gegevens moeten worden verwerkt.
> 
> 

Open Power BI met uw werk- of schoolaccount en selecteer de gegevensset en tabel die u hebt gedefinieerd als de uitvoer van de taak Stream Analytics.

![Selecteer in Power BI uw gegevensset en velden.](./media/export-stream-analytics/200.png)

Nu u deze gegevensset gebruiken in rapporten en dashboards in [Power BI.](https://powerbi.microsoft.com)

![Selecteer in Power BI uw gegevensset en velden.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>Zijn er geen gegevens?
* Controleer of u [de datumnotatie](#set-path-prefix-pattern) correct instelt op YYYY-MM-DD (met streepjes).

## <a name="video"></a>Video
Noam Ben Zeev laat zien hoe geëxporteerde gegevens verwerkt kunnen worden met Behulp van Stream Analytics.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Volgende stappen
* [Continue export](export-telemetry.md)
* [Gedetailleerde gegevensmodelreferentie voor de eigenschapstypen en -waarden.](export-data-model.md)
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)

