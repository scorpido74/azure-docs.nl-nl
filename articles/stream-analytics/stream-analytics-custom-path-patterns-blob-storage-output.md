---
title: Aangepaste BLOB-uitvoer partitioneren Azure Stream Analytics
description: In dit artikel worden de aangepaste paden voor het pad naar de tijd en de aangepaste velden of kenmerken van de Blob Storage-uitvoer van Azure Stream Analytics-taken beschreven.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: e978771eaafafe4120f9eec802525c293fb9c7c9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426385"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Aangepaste BLOB-uitvoer partitioneren Azure Stream Analytics

Azure Stream Analytics ondersteunt aangepaste BLOB-uitvoer partities met aangepaste velden of kenmerken en aangepaste DateTime-paden. 

## <a name="custom-field-or-attributes"></a>Aangepaste velden of kenmerken

Aangepaste veld-of invoer kenmerken verbeteren stroomafwaartse werk stromen voor gegevens verwerking en rapporten door meer controle over de uitvoer toe te staan.

### <a name="partition-key-options"></a>Opties voor partitie sleutels

De partitie sleutel of kolom naam die wordt gebruikt voor het partitioneren van invoer gegevens mag alfanumerieke tekens bevatten met afbreek streepjes, onderstrepingen en spaties. Het is niet mogelijk om geneste velden te gebruiken als partitie sleutel, tenzij u deze gebruikt in combi natie met aliassen. De partitie sleutel moet NVARCHAR (MAX) zijn.

### <a name="example"></a>Voorbeeld

Stel dat een taak invoer gegevens ontvangt van Live gebruikers sessies die zijn verbonden met een externe video game service, waarbij opgenomen gegevens een kolom bevatten **client_id** om de sessies te identificeren. Als u de gegevens wilt partitioneren op **client_id**, stelt u het veld patroon van BLOB-pad in om een partitie token **{client_id}** op te nemen in BLOB-uitvoer eigenschappen bij het maken van een taak. Als gegevens met verschillende **client_id** waarden door de stream Analytics taak worden uitgevoerd, worden de uitvoer gegevens opgeslagen in afzonderlijke mappen op basis van één **client_id** waarde per map.

![Pad patroon met client-id](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

En als de taak invoer sensor gegevens van miljoenen Sens oren bevat, waarbij elke sensor een **sensor_id**heeft, zou het patroon van het pad **{sensor_id}** zijn voor het partitioneren van elke sensor gegevens naar andere mappen.  


Met behulp van de REST API kan het gedeelte uitvoer van een JSON-bestand dat voor de aanvraag wordt gebruikt, er als volgt uitzien:  

![REST API uitvoer](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Zodra de taak wordt uitgevoerd, kan de container *clients* er als volgt uitzien:  

![Container clients](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Elke map kan meerdere blobs bevatten waarbij elke Blob een of meer records bevat. In het bovenstaande voor beeld bevindt zich één Blob in een 06000000 map met de volgende inhoud:

![Inhoud van BLOB](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

U ziet dat elke record in de BLOB een **client_id** kolom heeft die overeenkomt met de mapnaam, aangezien de kolom die wordt gebruikt voor het partitioneren van de uitvoer in het uitvoerpad, is **client_id**.

### <a name="limitations"></a>Beperkingen

1. Er is slechts één aangepaste partitie sleutel toegestaan in de BLOB-uitvoer eigenschap van het pad patroon. Alle volgende paden zijn geldig:

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData} 
   
2. Partitie sleutels zijn niet hoofdletter gevoelig, waardoor partitie sleutels als ' John ' en ' John ' gelijkwaardig zijn. Expressies kunnen ook niet worden gebruikt als partitie sleutels. Bijvoorbeeld: **{Columna + columnB}** werkt niet.  

3. Wanneer een invoer stroom bestaat uit records met een kardinaliteit van de partitie sleutel onder 8000, worden de records toegevoegd aan bestaande blobs en worden alleen nieuwe blobs gemaakt wanneer dat nodig is. Als de kardinaliteit meer dan 8000 is, is er geen garantie dat er bestaande blobs worden geschreven naar en er geen nieuwe blobs worden gemaakt voor een wille keurig aantal records met dezelfde partitie sleutel.

## <a name="custom-datetime-path-patterns"></a>Patronen voor aangepaste DateTime-paden

Aangepaste datum/tijd-padpatronen kunnen u opgeven de indeling van een uitvoer die overeenstemt met Hive-Streaming-conventies, waardoor Azure Stream Analytics om gegevens te verzenden naar Azure HDInsight en Azure Databricks voor downstream verwerken. Aangepaste datum/tijd-padpatronen eenvoudig worden geïmplementeerd met behulp van de `datetime` sleutelwoord in het veld pad naar het voorvoegsel van de uitvoer, samen met de indelingsopgave blob. Bijvoorbeeld `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Ondersteunde tokens

De volgende indeling aanduiding tokens kunnen zelfstandig of in combinatie worden gebruikt om aangepaste datum/tijd-notatie:

|Indelingsopgave   |Beschrijving   |De resultaten in het voorbeeld van de tijd 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|Een getal van vier cijfers van het jaar|2018|
|{datetime:MM}|Maand vanaf 01 en 12|01|
|{datetime:M}|Maand van 1 tot en met 12|1|
|{datetime:dd}|Dag vanaf 01 tot en met 31|02|
|{datetime:d}|Dag van 1 tot en met 12|2|
|{datetime:HH}|Met behulp van de 24-uurs tijdnotatie, tussen 00 en 23 uur|10|
|{datetime:mm}|Minuten van 00 24 uur per dag|06|
|{datetime:m}|Minuten van 0 tot en met 24|6|
|{datetime:ss}|Seconden tussen 00 en 60|08|

Als u niet gebruiken van aangepaste datum/tijd-patronen wilt, u kunt toevoegen van de {date} en/of {time}-token voor het voorvoegsel voor het pad voor het genereren van een vervolgkeuzelijst met ingebouwde notaties voor datum/tijd.

![Stream Analytics oude datum-/ tijdindelingen](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Uitbreidbaarheid en beperkingen

U kunt zoveel tokens `{datetime:<specifier>}`, zoals u in het padpatroon wilt, totdat het voorvoegsel van pad tekenlimiet is bereikt. Indeling weergaven kunnen niet worden gecombineerd in een enkele token buiten de al vermeld door de datum en tijd vervolgkeuzelijsten combinaties. 

Voor de partitie van een pad van `logs/MM/dd`:

|Geldige expressie   |Ongeldige expressie   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

U kunt de dezelfde indelingsopgave meerdere keren gebruiken in het voorvoegsel voor het pad. Het token moet telkens worden herhaald.

### <a name="hive-streaming-conventions"></a>Conventies voor hive-Streaming

Aangepaste padpatronen voor blob-opslag kunnen worden gebruikt met de Hive-Streaming-overeenkomst, die wordt verwacht dat de mappen die moeten worden gelabeld met `column=` in naam van de map.

Bijvoorbeeld `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Aangepaste uitvoer wordt voorkomen dat de moeite van het wijzigen van tabellen en partities handmatig toe te voegen aan poortgegevens tussen Azure Stream Analytics en Hive. Veel mappen kunnen in plaats daarvan worden toegevoegd automatisch met behulp van:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Voorbeeld

Maken van een storage-account, een resourcegroep, een Stream Analytics-taak en een invoerbron volgens de [Azure Stream Analytics-Azure Portal](stream-analytics-quick-create-portal.md) snelstartgids. Gebruik de dezelfde voorbeeldgegevens gebruikt in de Quick Start guide, die ook beschikbaar in de [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Maak een uitvoerlocatie blob met de volgende configuratie:

![Stream Analytics maken uitvoerlocatie blob](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Het patroon volledig pad is als volgt:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Wanneer u de taak start, wordt de mapstructuur van een op basis van het padpatroon gemaakt in uw blobcontainer. U kunt inzoomen op het niveau van de dag.

![Stream Analytics blob uitvoer met de aangepaste padpatroon](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Volgende stappen

* [Inzicht in de uitvoer van Azure Stream Analytics](stream-analytics-define-outputs.md)
