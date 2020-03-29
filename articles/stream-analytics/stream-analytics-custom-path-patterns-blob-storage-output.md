---
title: Aangepaste blob-uitvoerpartities van Azure Stream Analytics
description: In dit artikel worden de aangepaste DateTime-padpatronen en de aangepaste veld- of kenmerkenfuncties voor blob-opslaguitvoer van Azure Stream Analytics-taken beschreven.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: e978771eaafafe4120f9eec802525c293fb9c7c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426385"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Aangepaste blob-uitvoerpartities van Azure Stream Analytics

Azure Stream Analytics ondersteunt aangepaste blob-uitvoerpartities met aangepaste velden of kenmerken en aangepaste DateTime-padpatronen. 

## <a name="custom-field-or-attributes"></a>Aangepast veld of kenmerken

Aangepaste veld- of invoerkenmerken verbeteren downstream-gegevensverwerkings- en rapportageworkflows door dat er meer controle over de uitvoer mogelijk is.

### <a name="partition-key-options"></a>Opties voor partitiesleutel

De partitiesleutel of kolomnaam die wordt gebruikt om invoergegevens te partitioneren, kan alfanumerieke tekens bevatten met koppeltekens, onderdoelpunten en spaties. Het is niet mogelijk om geneste velden als partitiesleutel te gebruiken, tenzij deze wordt gebruikt in combinatie met aliassen. De partitiesleutel moet NVARCHAR(MAX) zijn.

### <a name="example"></a>Voorbeeld

Stel dat een taak invoergegevens bevat van live gebruikerssessies die zijn gekoppeld aan een externe videogameservice waarbij ingenomen gegevens een kolom **bevatten client_id** om de sessies te identificeren. Als u de gegevens wilt verdelen op **client_id,** stelt u het veld Blobpadpatroon in om een partitietoken **{client_id}** op te nemen in blobuitvoereigenschappen bij het maken van een taak. Terwijl gegevens met verschillende **client_id** waarden door de taak Stream Analytics stromen, worden de uitvoergegevens opgeslagen in afzonderlijke mappen op basis van één **client_id** waarde per map.

![Padpatroon met client-id](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Als de taakinvoer sensorgegevens was van miljoenen sensoren waar elke sensor een **sensor_id**had, zou het padpatroon **{sensor_id}** zijn om elke sensorgegevens te verdelen in verschillende mappen.  


Met de REST API kan de uitvoersectie van een JSON-bestand dat voor dat verzoek wordt gebruikt, er als volgt uitzien:  

![REST API-uitvoer](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Zodra de taak wordt uitgevoerd, kan de container *clients* er als volgt uitzien:  

![Clients container](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Elke map kan meerdere blobs bevatten waarbij elke blob een of meer records bevat. In het bovenstaande voorbeeld is er één blob in een map met het label "06000000" met de volgende inhoud:

![Blob-inhoud](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Elke record in de blob heeft een **client_id** kolom die overeenkomt met de mapnaam, omdat de kolom die wordt gebruikt om de uitvoer in het uitvoerpad te partitioneren **client_id**is.

### <a name="limitations"></a>Beperkingen

1. Er is slechts één aangepaste partitiesleutel toegestaan in de eigenschap Path Pattern blob output. Alle volgende padpatronen zijn geldig:

   * cluster1/{datum}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{datum}/{time}/{aFieldInMyData} 
   
2. Partitiesleutels zijn hoofdletters ongevoelig, dus partitiesleutels zoals 'John' en 'john' zijn gelijkwaardig. Ook kunnen expressies niet worden gebruikt als partitiesleutels. **{columnA + columnB}** werkt bijvoorbeeld niet.  

3. Wanneer een invoerstroom bestaat uit records met een partitiesleutelhoofddaliteit onder 8000, worden de records toegevoegd aan bestaande blobs en worden ze alleen nieuwe blobs gemaakt wanneer dat nodig is. Als de kardinaliteit meer dan 8000 is, is er geen garantie dat bestaande blobs worden geschreven en dat er geen nieuwe blobs worden gemaakt voor een willekeurig aantal records met dezelfde partitiesleutel.

## <a name="custom-datetime-path-patterns"></a>Aangepaste datetime-padpatronen

Met aangepaste DateTime-padpatronen u een uitvoerindeling opgeven die aansluit bij Hive Streaming-conventies, waardoor Azure Stream Analytics gegevens naar Azure HDInsight en Azure Databricks kan verzenden voor downstreamverwerking. Aangepaste DateTime-padpatronen kunnen eenvoudig `datetime` worden geïmplementeerd met het trefwoord in het veld Padvoorvoegsel van uw blob-uitvoer, samen met de indelingsspecificatie. Bijvoorbeeld `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Ondersteunde tokens

De volgende formaataanduidingstokens kunnen alleen of in combinatie worden gebruikt om aangepaste DateTime-indelingen te bereiken:

|Specificatie opmaken   |Beschrijving   |Resultaten op voorbeeldtijd 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|Het jaar als een viercijferig getal|2018|
|{datetime:MM}|Maand van 01 tot 12|01|
|{datetime:M}|Maand van 1 tot 12|1|
|{datetime:dd}|Dag van 01 tot 31|02|
|{datetime:d}|Dag van 1 tot 12|2|
|{datetime:HH}|Uur met de 24-uursnotatie, van 00 tot 23|10|
|{datetime:mm}|Minuten van 00 tot 24|06|
|{datetime:m}|Minuten van 0 tot 24|6|
|{datetime:ss}|Seconden van 00 tot 60|08|

Als u geen aangepaste DateTime-patronen wilt gebruiken, u het token {date} en/of {time} toevoegen aan het padvoorvoegsel om een vervolgkeuzelijst te genereren met ingebouwde DateTime-indelingen.

![Oude DateTime-indelingen van Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Uitbreidbaarheid en beperkingen

Je zoveel tokens `{datetime:<specifier>}`gebruiken als je wilt in het padpatroon totdat je de padvoorvoeging hebt bereikt. Indelingsaanduidingen kunnen niet worden gecombineerd binnen één token buiten de combinaties die al zijn vermeld op basis van de datum- en tijdsneerzetdropdowns. 

Voor een padpartitie van `logs/MM/dd`:

|Geldige expressie   |Ongeldige expressie   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

U dezelfde indelingsaanduiding meerdere keren gebruiken in het padvoorvoegsel. Het token moet elke keer worden herhaald.

### <a name="hive-streaming-conventions"></a>Hive Streaming conventies

Aangepaste padpatronen voor blobopslag kunnen worden gebruikt met de Hive Streaming-conventie, die verwacht dat mappen worden gelabeld met `column=` de naam van de map.

Bijvoorbeeld `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Aangepaste uitvoer elimineert het gedoe van het wijzigen van tabellen en het handmatig toevoegen van partities aan poortgegevens tussen Azure Stream Analytics en Hive. In plaats daarvan kunnen veel mappen automatisch worden toegevoegd met behulp van:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Voorbeeld

Maak een opslagaccount, een brongroep, een Stream Analytics-taak en een invoerbron volgens de quickstart-handleiding [azure portal van Azure Stream Analytics.](stream-analytics-quick-create-portal.md) Gebruik dezelfde voorbeeldgegevens die worden gebruikt in de quickstart-handleiding, die ook beschikbaar is op [GitHub.](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)

Maak een blobuitvoersink met de volgende configuratie:

![Stream Analytics maakt blob-uitvoersink](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Het volledige padpatroon ziet er als volgt uit:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Wanneer u de taak start, wordt er een mapstructuur gemaakt op basis van het padpatroon in de blobcontainer. U inzoomen op het dagniveau.

![Stream Analytics blob output met aangepast padpatroon](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Volgende stappen

* [Inzicht krijgen in uitvoer vanuit Azure Stream Analytics](stream-analytics-define-outputs.md)
