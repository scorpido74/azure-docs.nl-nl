---
title: Zoeken in blobs voor tekst zonder opmaak
titleSuffix: Azure Cognitive Search
description: Een zoek Indexeer functie configureren om platte tekst uit Azure-blobs te halen voor Zoek opdrachten in volledige tekst in azure Cognitive Search.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 2b01b9a3782d25660462d0cc2bf3aec87baf0023
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403785"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Blobs met tekst zonder opmaak indexeren in azure Cognitive Search

Wanneer u een [BLOB Indexeer functie](search-howto-indexing-azure-blob-storage.md) gebruikt om Doorzoek bare tekst te extra heren voor zoeken in volledige tekst, kunt u verschillende parserings modi aanroepen om betere index resultaten te krijgen. Standaard worden door de Indexeer functie gescheiden tekst-blobs geparseerd als één tekst segment. Als alle blobs echter tekst zonder opmaak bevatten met dezelfde code ring, kunt u de index prestaties aanzienlijk verbeteren met behulp van de modus voor het **parseren van tekst**.

## <a name="set-up-plain-text-indexing"></a>Indexering voor tekst zonder opmaak instellen

Als u de blobs voor tekst zonder opmaak wilt indexeren, maakt of werkt u een Indexeer functie definitie met de `parsingMode` configuratie-eigenschap in `text` op een aanvraag voor het maken van een [Indexeer functie](/rest/api/searchservice/create-indexer) :

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

De `UTF-8` code ring wordt standaard gebruikt. Als u een andere code ring wilt opgeven, gebruikt u de `encoding` configuratie-eigenschap: 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

## <a name="request-example"></a>Voor beeld van aanvraag

De parserings modi worden opgegeven in de definitie van de Indexeer functie.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-plaintext-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }
```

## <a name="help-us-make-azure-cognitive-search-better"></a>Help ons Azure Cognitive Search beter te maken
Als u een functie verzoek of ideeën voor verbeteringen hebt, geeft u uw invoer op [UserVoice](https://feedback.azure.com/forums/263029-azure-search/)op. Als u hulp nodig hebt bij het gebruik van de bestaande functie, plaatst u uw vraag op [stack overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="see-also"></a>Zie ook

* [Indexeerfuncties in Azure Cognitive Search](search-indexer-overview.md)
* [Een BLOB-Indexeer functie configureren](search-howto-indexing-azure-blob-storage.md)
* [Overzicht van BLOB-indexering](search-blob-storage-integration.md)