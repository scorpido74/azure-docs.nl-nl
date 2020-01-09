---
title: 'Zelf studie: afwijkingen visualiseren met batch detectie en Power BI'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de Anomaliey detector API en Power BI voor het visualiseren van afwijkingen in uw tijdreeks gegevens.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 1d1f71ed56138f2c799c7410508c0bc4899e7887
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448857"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Zelf studie: afwijkingen visualiseren met batch detectie en Power BI

Gebruik deze zelf studie om afwijkingen in een tijdreeks gegevensverzameling te vinden als een batch. Met Power BI bureau blad gaat u een Excel-bestand maken, de gegevens voorbereiden voor de anomalie detectie-API en statistische afwijkingen visualiseren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Power BI Desktop gebruiken voor het importeren en transformeren van een gegevensset voor een tijd reeks
> * Power BI Desktop integreren met de anomalie detectie-API voor het opsporen van batch afwijkingen
> * U kunt afwijkingen in uw gegevens visualiseren, waaronder verwachte en weer gegeven waarden en grenzen voor afwijkings detectie.

## <a name="prerequisites"></a>Vereisten

* [Micro soft power bi Desktop](https://powerbi.microsoft.com/get-started/)gratis beschikbaar.
* Een Excel-bestand (. XLSX) dat gegevens punten van de tijd reeks bevat. De voorbeeld gegevens voor deze Quick Start vindt u op [github](https://go.microsoft.com/fwlink/?linkid=2090962)

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>De time series-gegevens laden en Format teren

Als u aan de slag wilt gaan, opent u Power BI Desktop en laadt u de tijdreeks gegevens die u hebt gedownload van de vereisten. Dit Excel-bestand bevat een reeks UTC-tijds tempels (Coordinated Universal Time) en waardeparen.  

> [!NOTE]
> Power BI kunt gebruikmaken van gegevens uit een groot aantal bronnen, zoals CSV-bestanden, SQL-data bases, Azure Blob-opslag en nog veel meer.  

Klik in het hoofd venster van Power BI Desktop op het lint **Start** . Open in de groep **externe gegevens** van het lint de vervolg keuzelijst **gegevens ophalen** en klik op **Excel**.

![Een afbeelding van de knop gegevens ophalen in Power BI](../media/tutorials/power-bi-get-data-button.png)

Nadat het dialoog venster wordt weer gegeven, gaat u naar de map waar u het bestand example. xlsx hebt gedownload en selecteert u het. Nadat het dialoog venster **Navigator** wordt weer gegeven, klikt u op **Blad1**en vervolgens op **bewerken**.

![Een afbeelding van het scherm voor de gegevens bron navigator in Power BI](../media/tutorials/navigator-dialog-box.png)

Power BI worden de tijds tempels in de eerste kolom geconverteerd naar een `Date/Time` gegevens type. Deze tijds tempels moeten worden geconverteerd naar tekst om te worden verzonden naar de anomalie detector-API. Als de Power Query Editor niet automatisch wordt geopend, klikt u op **Query's bewerken** op het tabblad Start. 

Klik op het lint **Transformeren** in de Editor van Power query. Open in de **kolom** groep de optie **gegevens type:** vervolg keuzelijst en selecteer **tekst**.

![Een afbeelding van het scherm voor de gegevens bron navigator in Power BI](../media/tutorials/data-type-drop-down.png)

Wanneer u een bericht ziet over het wijzigen van het kolom Type, klikt u op **huidige vervangen**. Klik daarna op **sluiten & Toep assen** of **Toep assen** in het lint **Start** . 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Een functie maken voor het verzenden van de gegevens en het format teren van het antwoord

Als u het gegevens bestand wilt opmaken en verzenden naar de anomalie detectie-API, kunt u een query aanroepen voor de tabel die hierboven is gemaakt. Open in de Power Query Editor op het lint **Start** de vervolg keuzelijst **nieuwe bron** en klik op **lege query**.

Zorg ervoor dat de nieuwe query is geselecteerd en klik vervolgens op **Geavanceerde editor**. 

![Een afbeelding van de knop ' Geavanceerde editor ' in Power BI](../media/tutorials/advanced-editor-screen.png)

Gebruik in de Geavanceerde editor het volgende Power Query M-fragment om de kolommen uit de tabel op te halen en te verzenden naar de API. Daarna maakt de query een tabel op basis van het JSON-antwoord en retourneert deze. Vervang de `apiKey` variabele door de geldige Anomaliey-API-sleutel en `endpoint` met uw eind punt. Nadat u de query hebt ingevoerd in de Geavanceerde editor, klikt u op **gereed**.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({
                    
                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),
    
    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

Roep de query aan op uw gegevens blad door `Sheet1` onderstaande **para meter**op te geven en op **aanroepen**te klikken. 

![Een afbeelding van de knop Geavanceerde editor](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Privacy en verificatie van gegevens bronnen

> [!NOTE]
> Houd rekening met het beleid van uw organisatie voor privacy en toegang tot gegevens. Zie [Power bi Desktop privacyniveaus](https://docs.microsoft.com/power-bi/desktop-privacy-levels) voor meer informatie.

Er wordt mogelijk een waarschuwing weer gegeven wanneer u probeert de query uit te voeren, omdat deze een externe gegevens bron gebruikt. 

![Een afbeelding met een waarschuwing die is gemaakt door Power BI](../media/tutorials/blocked-function.png)

Klik op **bestand**en kies **Opties en instellingen**om dit probleem op te lossen. Klik vervolgens op **Opties**. Selecteer onder **Huidig bestand**de optie **Privacy**en **Negeer de privacyniveaus en mogelijk verbeterde prestaties**. 

Daarnaast kunt u een bericht ontvangen waarin u wordt gevraagd hoe u verbinding wilt maken met de API.

![Een afbeelding met een aanvraag voor het opgeven van toegangs referenties](../media/tutorials/edit-credentials-message.png)

Klik op **referenties bewerken** in het bericht om dit probleem op te lossen. Nadat het dialoog venster wordt weer gegeven, selecteert u **anoniem** om verbinding met de API anoniem te maken. Klik vervolgens op **Verbinden**. 

Klik daarna op **sluiten & Toep assen** op het lint **Start** om de wijzigingen toe te passen.

## <a name="visualize-the-anomaly-detector-api-response"></a>Het antwoord voor de anomalie detectie-API visualiseren

Begin in het hoofd venster van Power BI de eerder gemaakte query's te gebruiken om de gegevens te visualiseren. Selecteer eerst **lijn diagram** in **Visualisaties**. Voeg vervolgens de tijds tempel van de aangeroepen functie toe aan de **as**van de lijn grafiek. Klik er met de rechter muisknop op en selecteer **tijds tempel**. 

![Klik met de rechter muisknop op de waarde van de tijds tempel](../media/tutorials/timestamp-right-click.png)

Voeg de volgende velden van de **aangeroepen functie** toe aan het veld **waarden** van de grafiek. Gebruik de onderstaande scherm afbeelding om uw grafiek te bouwen.

    * Waarde
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![Een afbeelding van het scherm nieuwe snelle meting](../media/tutorials/chart-settings.png)

Nadat u de velden hebt toegevoegd, klikt u op het diagram en past u het formaat aan om alle gegevens punten weer te geven. Uw grafiek ziet er ongeveer als volgt uit:

![Een afbeelding van het scherm nieuwe snelle meting](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Afwijkende gegevens punten weer geven

Klik aan de rechter kant van het Power BI venster onder het deel venster **velden** met de rechter muisknop op **waarde** onder de **aangeroepen functie query**en klikt u op **nieuwe snelle meting**.

![Een afbeelding van het scherm nieuwe snelle meting](../media/tutorials/new-quick-measure.png)

Selecteer op het scherm dat wordt weer gegeven **gefilterde waarde** als berekening. Stel de **basis waarde** in op `Sum of Value`. Sleep `IsAnomaly` van de **aangeroepen functie** velden om te **filteren**. Selecteer `True` in de vervolg keuzelijst **filter** .

![Een afbeelding van het scherm nieuwe snelle meting](../media/tutorials/new-quick-measure-2.png)

Nadat u op **OK**hebt geklikt, wordt er een `Value for True` veld weer geven onder aan de lijst met velden. Klik er met de rechter muisknop op en wijzig de naam ervan in **afwijkingen**. Voeg deze toe aan de **waarden**van de grafiek. Selecteer vervolgens het hulp programma **opmaken** en stel het type X-as in op **categorische**.

![Een afbeelding van het scherm nieuwe snelle meting](../media/tutorials/format-x-axis.png)

Pas kleuren toe op uw grafiek door te klikken op het hulp programma **opmaken** en **gegevens kleuren**. De grafiek moet er ongeveer als volgt uitzien:

![Een afbeelding van het scherm nieuwe snelle meting](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Anomalie detectie met Azure Databricks streamen](anomaly-detection-streaming-databricks.md)
