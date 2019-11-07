---
title: CSV-blobs indexeren met behulp van de delimitedText-verwerkings modus (preview)
titleSuffix: Azure Cognitive Search
description: Pak CSV-bestand uit vanuit Azure Blob-opslag met behulp van de delimitedText-verwerkings modus, momenteel beschikbaar als open bare preview.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4edeb8d535504c305319aad35637bb1b09f65984
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719248"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Het indexeren van CSV-blobs met behulp van de delimitedText-verwerkings modus en BLOB-Indexeer functies in azure Cognitive Search 

> [!IMPORTANT] 
> De delimitedText-verwerkings modus is momenteel beschikbaar als open bare preview. De Preview-functionaliteit wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is momenteel geen portal-of .NET SDK-ondersteuning.

Standaard parseert [Azure Cognitive Search BLOB-indexering](search-howto-indexing-azure-blob-storage.md) gescheiden tekst-blobs als één tekst segment. Met blobs die CSV-gegevens bevatten, wilt u echter vaak elke regel in de BLOB behandelen als een afzonderlijk document. Als u bijvoorbeeld de volgende tekst met scheidings tekens wilt, kunt u deze in twee documenten parseren, elk met de velden ' id ', ' datePublished ' en ' Tags ': 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

In dit artikel leert u hoe u CSV-blobs kunt parseren met een Azure Cognitive Search BLOB-indexer door de `delimitedText`-verwerkings modus in te stellen. 

> [!NOTE]
> Volg de aanbevelingen voor de configuratie van de Indexeer functie in [een-op-veel-indexering](search-howto-index-one-to-many-blobs.md) om meerdere zoek documenten uit één Azure-Blob af te voeren.

## <a name="setting-up-csv-indexing"></a>CSV-indexering instellen
Als u CSV-blobs wilt indexeren, maakt of bijwerkt u een Indexeer functie definitie met de `delimitedText`-parserings modus op een aanvraag voor het maken van een [Indexeer functie](https://docs.microsoft.com/rest/api/searchservice/create-indexer) :

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` geeft aan dat de eerste (niet-lege) regel van elke BLOB kopteksten bevat.
Als blobs geen oorspronkelijke header regel bevatten, moeten de headers worden opgegeven in de Indexeer functie: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

U kunt het scheidings teken aanpassen met behulp van de configuratie-instelling `delimitedTextDelimiter`. Bijvoorbeeld:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Op dit moment wordt alleen de UTF-8-code ring ondersteund. Als u ondersteuning voor andere code ringen nodig hebt, stem u dan op [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Wanneer u de modus voor het parseren van tekst met scheidings tekens gebruikt Cognitive Search, wordt ervan uitgegaan dat alle blobs in uw gegevens bron CSV zijn. Als u een combi natie van CSV-en niet-CSV-blobs in dezelfde gegevens bron wilt ondersteunen, moet u deze op [UserVoice](https://feedback.azure.com/forums/263029-azure-search)stemmen.
> 
> 

## <a name="request-examples"></a>Aanvraag voorbeelden
Als u dit alles gebruikt, zijn hier de volledige voor beelden van payload. 

Bron 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexeer functie

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-cognitive-search-better"></a>Help ons Azure Cognitive Search beter te maken
Als u een functie verzoek of ideeën voor verbeteringen hebt, geeft u uw invoer op [UserVoice](https://feedback.azure.com/forums/263029-azure-search/)op.

