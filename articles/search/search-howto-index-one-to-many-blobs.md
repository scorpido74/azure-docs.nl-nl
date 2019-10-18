---
title: Eén BLOB indexeren in veel zoek index documenten van Azure Blob indexer voor zoeken in volledige tekst
description: Verken Azure-blobs voor tekst inhoud met behulp van de indexer van Azure Search blob. Elke Blob kan een of meer Azure Search index documenten opleveren.
ms.date: 05/02/2019
author: arv100kri
manager: nitinme
ms.author: arjagann
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.openlocfilehash: 585d1e64ae124dce8cb0d4165ecbf0f503560405
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533694"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Blobs indexeren om meerdere zoek documenten te maken
Een BLOB-Indexeer functie behandelt standaard de inhoud van een BLOB als één Zoek document. Bepaalde **parsingMode** -waarden ondersteunen scenario's waarbij een afzonderlijke Blob kan leiden tot meerdere zoek documenten. De verschillende soorten **parsingMode** waarmee een Indexeer functie meer dan één Zoek document uit een BLOB kan extra heren:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Een-op-veel-document sleutel
Elk document dat in een Azure Search index wordt weer gegeven, wordt uniek geïdentificeerd door een document sleutel. 

Als er geen parserings modus is opgegeven, en als er geen expliciete toewijzing voor het sleutel veld in de index is Azure Search [wijst](search-indexer-field-mappings.md) de `metadata_storage_path` eigenschap automatisch toe als de sleutel. Deze toewijzing zorgt ervoor dat elke BLOB wordt weer gegeven als een afzonderlijk Zoek document.

Wanneer u een van de hierboven genoemde parserings modi gebruikt, wordt één BLOB toegewezen aan veel Zoek documenten, waardoor een document sleutel alleen op basis van BLOB-meta gegevens ongeschikt wordt gemaakt. Om deze beperking te overwinnen, kan Azure Search een ' één-op-veel ' document sleutel genereren voor elke afzonderlijke entiteit die uit een blob is geëxtraheerd. Deze eigenschap heet `AzureSearch_DocumentKey` en wordt toegevoegd aan elke afzonderlijke entiteit die uit de blob is geëxtraheerd. De waarde van deze eigenschap is gegarandeerd uniek voor elke afzonderlijke entiteit _in blobs_ en de entiteiten worden weer gegeven als afzonderlijke Zoek documenten.

Wanneer er geen expliciete veld toewijzingen voor het sleutel index veld worden opgegeven, wordt de `AzureSearch_DocumentKey` standaard aan de toewijzing toegewezen, met behulp van de `base64Encode` veld toewijzings functie.

## <a name="example"></a>Voorbeeld
Stel dat u een index definitie hebt met de volgende velden:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

En uw BLOB-container heeft blobs met de volgende structuur:

_Blob1. json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2. json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Wanneer u een Indexeer functie maakt en de **parsingMode** instelt op `jsonLines`-zonder expliciete veld toewijzingen voor het sleutel veld op te geven, wordt de volgende toewijzing impliciet toegepast
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Deze installatie heeft tot gevolg dat de Azure Search index met de volgende informatie (met base64 versleutelde id verkort voor de boog)

| id | temperatuur | pressure | tijdstempel |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Aangepaste veld toewijzing voor index sleutel veld

Uitgaande van dezelfde index definitie als in het vorige voor beeld, zeggen uw BLOB-container blobs met de volgende structuur:

_Blob1. json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2. json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Wanneer u een Indexeer functie met `delimitedText` **parsingMode**maakt, kan het natuurlijk handig zijn om een veld toewijzings functie als volgt in te stellen op het sleutel veld:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Deze toewijzing resulteert echter _niet_ in vier documenten die in de index worden weer gegeven, omdat het veld `recordid` niet uniek is in _blobs_. Daarom raden we u aan het gebruik van de impliciete veld toewijzing van de eigenschap `AzureSearch_DocumentKey` toe te passen op het sleutel index veld voor een-op-veel-parserings modus.

Als u een expliciete veld toewijzing wilt instellen, moet u ervoor zorgen dat de _sourceField_ uniek is voor elke afzonderlijke entiteit **in alle blobs**.

> [!NOTE]
> De benadering die wordt gebruikt door `AzureSearch_DocumentKey` om ervoor te zorgen dat uniekheid per geëxtraheerde entiteit kan worden gewijzigd en daarom moet u niet vertrouwen op de waarde voor de behoeften van uw toepassing.

## <a name="next-steps"></a>Volgende stappen

Als u nog niet bekend bent met de basis structuur en werk stroom voor het indexeren van blobs, moet u eerst de [indexering van Azure Blob Storage met Azure Search](search-howto-index-json-blobs.md) controleren. Raadpleeg de volgende artikelen voor meer informatie over het parseren van modi voor verschillende BLOB Conté-typen.

> [!div class="nextstepaction"]
> [Indexeren van CSV-blobs](search-howto-index-csv-blobs.md) 
>  het[indexeren van JSON-blobs](search-howto-index-json-blobs.md)