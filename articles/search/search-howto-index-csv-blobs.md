---
title: CSV-blobs indexeren met Azure Search BLOB-indexer-Azure Search
description: CSV-blobs in Azure Blob-opslag verkennen voor Zoek opdrachten in volledige tekst met behulp van een Azure Search index. Indexeer functies automatiseren gegevens opname voor geselecteerde gegevens bronnen, zoals Azure Blob-opslag.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: b135fd1a0758567a7b504996bf442a913741fe59
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656758"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indexeren van CSV-blobs met Azure Search BLOB-Indexeer functie

> [!Note]
> de delimitedText-verwerkings modus is een preview-versie en is niet bedoeld voor productie gebruik. De [rest API versie 2019-05-06-preview](search-api-preview.md) biedt deze functie. Er is op dit moment geen .NET SDK-ondersteuning.
>

[Azure Search BLOB-Indexeer functie](search-howto-indexing-azure-blob-storage.md) parseert standaard blobs met scheidings tekens als één tekst segment. Met blobs die CSV-gegevens bevatten, wilt u echter vaak elke regel in de BLOB behandelen als een afzonderlijk document. Als u bijvoorbeeld de volgende tekst met scheidings tekens wilt, kunt u deze in twee documenten parseren, elk met de velden ' id ', ' datePublished ' en ' Tags ': 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

In dit artikel vindt u informatie over het parseren van CSV-blobs met een Azure Search BLOB indexerby `delimitedText` instellen van de verdeel modus. 

> [!NOTE]
> Volg de aanbevelingen voor de configuratie van de Indexeer functie in [een-op-veel](search-howto-index-one-to-many-blobs.md) -indexering om meerdere zoek documenten uit één Azure-Blob af te voeren.

## <a name="setting-up-csv-indexing"></a>CSV-indexering instellen
Als u CSV-blobs wilt indexeren, maakt of werkt u een Indexeer `delimitedText` functie definitie met de parserings modus op een aanvraag voor het maken van een [Indexeer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) functie:

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders`geeft aan dat de eerste (niet-lege) regel van elke BLOB kopteksten bevat.
Als blobs geen oorspronkelijke header regel bevatten, moeten de headers worden opgegeven in de Indexeer functie: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

U kunt het scheidings teken aanpassen met `delimitedTextDelimiter` behulp van de configuratie-instelling. Bijvoorbeeld:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Op dit moment wordt alleen de UTF-8-code ring ondersteund. Als u ondersteuning voor andere code ringen nodig hebt, stem u dan op [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Wanneer u de modus voor het parseren van tekst met scheidings tekens gebruikt Azure Search, wordt ervan uitgegaan dat alle blobs in uw gegevens bron CSV zijn. Als u een combi natie van CSV-en niet-CSV-blobs in dezelfde gegevens bron wilt ondersteunen, moet u deze op [UserVoice](https://feedback.azure.com/forums/263029-azure-search)stemmen.
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

## <a name="help-us-make-azure-search-better"></a>Help ons Azure Search beter te maken
Als u een functie verzoek of ideeën voor verbeteringen hebt, geeft u uw invoer op [UserVoice](https://feedback.azure.com/forums/263029-azure-search/)op.

