---
title: Zoeken via CSV-blobs
titleSuffix: Azure Cognitive Search
description: Csv uit Azure Blob-opslag extraheren en importeren met behulp van de delimitedText-parsingmodus.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bf600890bfed570e712a159005b8ef5267298cc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122318"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>CSV-blobs indexeren met de limitedText-parsingmodus en Blob-indexen in Azure Cognitive Search

[Azure Cognitive Search blob indexer](search-howto-indexing-azure-blob-storage.md) parses ontlast standaard afgebakende tekstblobs als één stuk tekst. Met blobs met CSV-gegevens wilt u echter vaak elke regel in de blob als een afzonderlijk document behandelen. Als u bijvoorbeeld de volgende afgebakende tekst ziet, u deze in twee documenten plaatsen, die elk 'id', 'datePublished' en 'tags'-velden bevatten: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

In dit artikel leert u hoe u CSV-blobs ontwijsmaken `delimitedText` met een Azure Cognitive Search blob-indexer door de parsing-modus in te stellen. 

> [!NOTE]
> Volg de aanbevelingen voor indexerconfiguratie in [Één-op-veel indexering](search-howto-index-one-to-many-blobs.md) om meerdere zoekdocumenten uit één Azure-blob uit te schakelen.

## <a name="setting-up-csv-indexing"></a>CSV-indexering instellen
Als u CSV-blobs wilt indexeren, `delimitedText` maakt of werkt u een indexerdefinitie samen met de parsingsmodus op een aanvrager [van Indexer maken:](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders`geeft aan dat de eerste (niet-lege) regel van elke blob kopteksten bevat.
Als blobs geen eerste koptekstlijn bevatten, moeten de kopteksten worden opgegeven in de indexerconfiguratie: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

U het tekensteken aanpassen `delimitedTextDelimiter` met de configuratie-instelling. Bijvoorbeeld:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Momenteel wordt alleen de UTF-8-codering ondersteund. Als u ondersteuning nodig hebt voor andere coderingen, stem dan voor deze op [UserVoice.](https://feedback.azure.com/forums/263029-azure-search)

> [!IMPORTANT]
> Wanneer u de beperkte tekstparsing-modus gebruikt, gaat Azure Cognitive Search ervan uit dat alle blobs in uw gegevensbron CSV zijn. Als u een mix van CSV- en niet-CSV-blobs in dezelfde gegevensbron moet ondersteunen, stem dan voor deze mix op [UserVoice.](https://feedback.azure.com/forums/263029-azure-search)
> 
> 

## <a name="request-examples"></a>Voorbeelden aanvragen
Om dit allemaal samen te stellen, hier zijn de volledige payload voorbeelden. 

Datasource: 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexer:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-cognitive-search-better"></a>Help ons Azure Cognitive Search beter te maken
Als u functieverzoeken of ideeën voor verbeteringen hebt, geeft u uw input op [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

