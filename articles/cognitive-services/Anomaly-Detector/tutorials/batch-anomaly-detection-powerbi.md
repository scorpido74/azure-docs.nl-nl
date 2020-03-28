---
title: 'Zelfstudie: Afwijkingen visualiseren met batchdetectie en Power BI'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de Anomaly Detector API en Power BI om afwijkingen in uw tijdreeksgegevens te visualiseren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: 1b486aaf0ce33e31433c2c3d0f7a1ff2c7089132
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78402659"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Zelfstudie: Afwijkingen visualiseren met batchdetectie en Power BI

Gebruik deze zelfstudie om afwijkingen te vinden binnen een tijdreeks gegevensset als een batch. Met Power BI-bureaublad neemt u een Excel-bestand, bereidt u de gegevens voor op de Api voor anomaliedetector en visualiseert u statistische afwijkingen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Power BI-bureaublad gebruiken om een tijdreeksgegevensset te importeren en te transformeren
> * Integreer Power BI Desktop met de Anomaly Detector API voor batchanomaliedetectie
> * Visualiseer afwijkingen die in uw gegevens worden gevonden, inclusief verwachte en geziene waarden, en anomaliedetectiegrenzen.

## <a name="prerequisites"></a>Vereisten
* Een [Azure-abonnement](https://azure.microsoft.com/free/)
* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), gratis beschikbaar.
* Een excelbestand (.xlsx) met tijdreeksgegevenspunten. De voorbeeldgegevens voor deze quickstart zijn te vinden op [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)
* Zodra u uw Azure-abonnement <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="hebt,"  target="_blank">maakt u <span class="docon docon-navigate-external x-hidden-focus"></span> </a> een Text Analytics-bron en maakt u een Text Analytics-bron in de Azure-portal om uw sleutel en eindpunt op te halen. 
    * U hebt de sleutel en het eindpunt van de resource die u maakt nodig om uw toepassing te verbinden met de Text Analytics API. Je doet dit later in de snelle start.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>De tijdreeksgegevens laden en opmaken

Open Power BI Desktop om aan de slag te gaan en laad de tijdreeksgegevens die u hebt gedownload van de vereisten. Dit excelbestand bevat een reeks Gecoördineerde universele tijdstempels en waardeparen.  

> [!NOTE]
> Power BI kan gegevens uit een breed scala aan bronnen gebruiken, zoals CSV-bestanden, SQL-databases, Azure blob-opslag en meer.  

Klik in het hoofdvenster van Power BI-bureaublad op het lint **Start.** Open in de groep **Externe gegevens** van het lint het vervolgkeuzemenu **Gegevens ophalen** en klik op **Excel**.

![Een afbeelding van de knop Gegevens downloaden in Power BI](../media/tutorials/power-bi-get-data-button.png)

Nadat het dialoogvenster is weergegeven, navigeert u naar de map waar u het voorbeeld .xlsx-bestand hebt gedownload en selecteert u het. Nadat de **dialoog met navigator** is weergegeven, klikt u op **Blad1**en **vervolgens bewerken**.

![Een afbeelding van het scherm 'Navigator' in Power BI](../media/tutorials/navigator-dialog-box.png)

Power BI converteert de tijdstempels in `Date/Time` de eerste kolom naar een gegevenstype. Deze tijdstempels moeten worden omgezet in tekst om naar de Api voor anomaliedetector te worden verzonden. Als de Power Query-editor niet automatisch wordt geopend, klikt u op **Query's bewerken** op het tabblad Start. 

Klik **op** het lint Transformeren in de Power Query Editor. Open in de groep **Een kolom** het vervolgkeuzemenu **Gegevenstype:** en selecteer **Tekst**.

![Een afbeelding van het scherm 'Navigator' in Power BI](../media/tutorials/data-type-drop-down.png)

Wanneer u een melding krijgt over het wijzigen van het kolomtype, klikt u op **Huidig vervangen**. Klik daarna op **Sluiten & toepassen** of toepassen **op** het lint **Start.** 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Een functie maken om de gegevens te verzenden en het antwoord op te maken

Als u het gegevensbestand wilt opmaken en verzenden naar de API voor anomaliedetector, u een query aanroepen in de bovenstaande tabel. Open in de Power Query Editor op het lint **Start** het vervolgkeuzemenu **Nieuwe bron** en klik op **Lege query**.

Controleer of uw nieuwe query is geselecteerd en klik vervolgens op **Geavanceerde editor**. 

![Een afbeelding van de knop 'Geavanceerde editor' in Power BI](../media/tutorials/advanced-editor-screen.png)

Gebruik in de geavanceerde editor het volgende Power Query M-fragment om de kolommen uit de tabel te extraheren en naar de API te verzenden. Daarna maakt de query een tabel uit het JSON-antwoord en retourneert deze. Vervang `apiKey` de variabele door uw geldige `endpoint` Api-sleutel voor anomaliedetector en door uw eindpunt. Nadat u de query hebt ingevoerd in de geavanceerde editor, klikt u op **Gereed**.

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

Roep de query op uw `Sheet1` gegevensblad aan door onder **Parameter invoeren te**selecteren en op **Aanroepen te klikken**. 

![Een afbeelding van de knop 'Geavanceerde editor'](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Privacy en verificatie van gegevensbronnen

> [!NOTE]
> Wees op de hoogte van het beleid van uw organisatie voor gegevensprivacy en -toegang. Zie [de privacyniveaus van Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-privacy-levels) voor meer informatie.

U een waarschuwingsbericht ontvangen wanneer u probeert de query uit te voeren, omdat deze een externe gegevensbron gebruikt. 

![Een afbeelding met een waarschuwing die is gemaakt door Power BI](../media/tutorials/blocked-function.png)

Als u dit wilt oplossen, klikt u op **Bestand**en **opties en instellingen**. Klik vervolgens op **Opties**. Selecteer **onder Huidig bestand**de optie **Privacy**en Negeer **de privacyniveaus en verbeter mogelijk de prestaties**. 

Daarnaast u een bericht ontvangen waarin u wordt gevraagd op te geven hoe u verbinding wilt maken met de API.

![Een afbeelding met een verzoek om toegangsreferenties op te geven](../media/tutorials/edit-credentials-message.png)

Als u dit wilt oplossen, klikt u op **Referenties bewerken** in het bericht. Nadat het dialoogvenster is weergegeven, selecteert u **Anoniem** om anoniem verbinding te maken met de API. Klik vervolgens op **Verbinden**. 

Klik daarna op **Sluiten & toepassen** op het lint **Start** om de wijzigingen toe te passen.

## <a name="visualize-the-anomaly-detector-api-response"></a>Visualiseer de API-respons van de Anomaliedetector

Begin in het hoofdscherm van Power BI met de bovenstaande query's om de gegevens te visualiseren. Selecteer eerst **Lijndiagram** in **visualisaties**. Voeg vervolgens de tijdstempel van de aangeroepen functie toe aan de **as**van het lijndiagram. Klik er met de rechtermuisknop op en selecteer **Timestamp**. 

![Met de rechtermuisknop op de waarde Tijdstempel klikken](../media/tutorials/timestamp-right-click.png)

Voeg de volgende velden van de **ingeroepen functie toe** aan het veld **Waarden** van de grafiek. Gebruik de onderstaande screenshot om te helpen bij het samenstellen van uw grafiek.

    * Waarde
    * Bovenmarges
    * Lagere marges
    * Verwachte waarden

![Een afbeelding van het nieuwe scherm met snelle meting](../media/tutorials/chart-settings.png)

Nadat u de velden hebt toegevoegd, klikt u op de grafiek en wijzigt u het formaat ervan om alle gegevenspunten weer te geven. Uw grafiek zal lijken op de onderstaande screenshot:

![Een afbeelding van het nieuwe scherm met snelle meting](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Afwijkende gegevenspunten weergeven

Klik aan de rechterkant van het Power BI-venster onder het deelvenster **VELDEN** met de rechtermuisknop op **Waarde** onder de **query Ingeroepen functie**en klik op Nieuwe snelle **meting**.

![Een afbeelding van het nieuwe scherm met snelle meting](../media/tutorials/new-quick-measure.png)

Selecteer Op het scherm dat wordt weergegeven **de optie Gefilterde waarde** als berekening. **Basiswaarde** instellen `Sum of Value`op . Sleep `IsAnomaly` vervolgens van de velden **Ingeroepen functie** naar **Filter**. Selecteer `True` in de vervolgkeuzelijst **Filter.**

![Een afbeelding van het nieuwe scherm met snelle meting](../media/tutorials/new-quick-measure-2.png)

Nadat u op **Ok**hebt `Value for True` geklikt, hebt u een veld, onder aan de lijst met velden. Klik er met de rechtermuisknop op en wijzig het in **Anomalie**. Voeg deze toe aan de **waarden**van de grafiek. Selecteer vervolgens het gereedschap **Opmaak** en stel het type X-as in **op Categorisch**.

![Een afbeelding van het nieuwe scherm met snelle meting](../media/tutorials/format-x-axis.png)

Kleur toepassen op de grafiek door te klikken op het gereedschap **Opmaak** en **De kleuren Gegevens**. Uw grafiek moet er als volgt uitzien:

![Een afbeelding van het nieuwe scherm met snelle meting](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Detectie van streaminganomalies met Azure Databricks](anomaly-detection-streaming-databricks.md)
