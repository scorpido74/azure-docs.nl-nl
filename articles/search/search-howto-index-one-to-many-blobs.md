---
title: Blobs indexeren met meerdere documenten
titleSuffix: Azure Cognitive Search
description: Azure-blobs crawlen voor tekstinhoud met de Azure Congitive Search Blob-indexer, waarbij elke blob mogelijk een of meer zoekindexdocumenten oplevert.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1840bda0ecc9462a5d8f796b616d728d0bb412f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112265"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Blobs indexeren om meerdere zoekdocumenten te produceren
Standaard behandelt een blob-indexer de inhoud van een blob als één zoekdocument. Bepaalde **parsingMode-waarden** ondersteunen scenario's waarin een afzonderlijke blob kan resulteren in meerdere zoekdocumenten. De verschillende typen **parsingMode** waarmee een indexeerder meer dan één zoekdocument uit een blob kan extraheren, zijn:
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>Een-op-veel documentsleutel
Elk document dat wordt weergegeven in een Azure Cognitive Search-index wordt uniek geïdentificeerd door een documentsleutel. 

Wanneer er geen parsing-modus is opgegeven en als er geen expliciete toewijzing is `metadata_storage_path` voor het sleutelveld in de index Azure Cognitive [Search, wordt](search-indexer-field-mappings.md) de eigenschap automatisch als de sleutel toegewezen. Deze toewijzing zorgt ervoor dat elke blob wordt weergegeven als een afzonderlijk zoekdocument.

Bij het gebruik van een van de hierboven genoemde parsingmodi wordt één blob toegewezen aan "veel" zoekdocumenten, waardoor een documentsleutel uitsluitend is gebaseerd op blob-metagegevens ongeschikt. Om deze beperking te overwinnen, is Azure Cognitive Search in staat om een 'één-op-veel'-documentsleutel te genereren voor elke afzonderlijke entiteit die uit een blob wordt gehaald. Deze eigenschap `AzureSearch_DocumentKey` heeft een naam en wordt toegevoegd aan elke afzonderlijke entiteit die uit de blob wordt gehaald. De waarde van deze eigenschap is gegarandeerd uniek voor elke afzonderlijke entiteit _in blobs_ en de entiteiten worden weergegeven als afzonderlijke zoekdocumenten.

Wanneer er standaard geen expliciete veldtoewijzingen voor het `AzureSearch_DocumentKey` sleutelindexveld zijn opgegeven, `base64Encode` wordt deze toegewezen aan het veld met behulp van de veldtoewijzingsfunctie.

## <a name="example"></a>Voorbeeld
Stel dat u een indexdefinitie hebt met de volgende velden:
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

En uw blobcontainer heeft blobs met de volgende structuur:

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

Wanneer u een indexermaakt en de `jsonLines` **parsingMode** instelt op - zonder expliciete veldtoewijzingen voor het sleutelveld op te geven, wordt de volgende toewijzing impliciet toegepast
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

Deze instelling resulteert in de Azure Cognitive Search-index met de volgende informatie (base64 gecodeerde id verkort voor beknoptheid)

| id | temperatuur | pressure | tijdstempel |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx YjIuanNvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy YjIuanNvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Aangepaste veldtoewijzing voor indexsleutelveld

Stel dat uw blobcontainer blobs heeft met de volgende structuur, uitgaande van dezelfde indexdefinitie als in het vorige voorbeeld:

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

Wanneer u een indexer met `delimitedText` **parsingMode**maakt, voelt het misschien natuurlijk om een veldtoewijzingsfunctie als volgt in te stellen op het sleutelveld:

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

Deze toewijzing resulteert echter _niet_ in 4 documenten die `recordid` in de index worden weergegeven, omdat het veld niet uniek is _voor blobs._ Daarom raden we u aan gebruik te maken `AzureSearch_DocumentKey` van de impliciete veldtoewijzing die van de eigenschap wordt toegepast op het veld sleutelindex voor "één-op-veel" parsingmodi.

Als u een expliciete veldtoewijzing wilt instellen, controleert u of het _sourceField_ voor elke afzonderlijke entiteit **voor alle blobs**verschilt.

> [!NOTE]
> De aanpak `AzureSearch_DocumentKey` die wordt gebruikt door te zorgen voor uniciteit per geëxtraheerde entiteit is onderhevig aan verandering en daarom moet u niet vertrouwen op de waarde ervan voor de behoeften van uw toepassing.

## <a name="next-steps"></a>Volgende stappen

Als u nog niet bekend bent met de basisstructuur en werkstroom van blobindexering, moet u Azure Blob Storage met [Azure Cognitive Search](search-howto-index-json-blobs.md) eerst controleren. Bekijk de volgende artikelen voor meer informatie over parsingmodi voor verschillende blob-inhoudstypen.

> [!div class="nextstepaction"]
> [CSV-blobs](search-howto-index-csv-blobs.md)
> [indexeren Json-blobs indexeren](search-howto-index-json-blobs.md)
