---
title: Het rangschikken van gelijkenisalgoritme
titleSuffix: Azure Cognitive Search
description: Hoe het gelijkenisalgoritme in te stellen om een nieuw gelijkenisalgoritme voor rangschikking te proberen
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: c327440649300533c94c2a1956e3c45f433c9780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409971"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Classificatiealgoritme in Azure Cognitive Search

> [!IMPORTANT]
> Vanaf 15 juli 2020 zullen nieuw opgerichte zoekservices gebruik maken van de BM25-rankingfunctie, die in de meeste gevallen heeft bewezen zoekrankings te bieden die beter aansluiten bij de verwachtingen van de gebruiker dan de huidige standaardrangschikking.  Naast de superieure rangschikking maakt BM25 ook configuratieopties mogelijk voor het afstemmen van resultaten op basis van factoren zoals de grootte van het document.  
>
> Met deze wijziging ziet u waarschijnlijk kleine veranderingen in het bestellen van uw zoekresultaten.   Voor degenen die de impact van deze wijziging willen testen, hebben we in de API 2019-05-06-Preview een mogelijkheid beschikbaar gesteld om BM25-scores op nieuwe indexen in te schakelen.  

In dit artikel wordt beschreven hoe u een service die vóór 15 juli 2020 is gemaakt, bijwerken om het nieuwe BM25-classificatiealgoritme te gebruiken.

Azure Cognitive Search zal gebruik maken van de officiële Lucene implementatie van de Okapi BM25 algoritme, *BM25Similarity*, die de eerder gebruikte *ClassicSimilarity* implementatie zal vervangen. Net als het oudere ClassicSimilarity-algoritme is BM25Similarity een TF-IDF-achtige ophaalfunctie die de term frequentie (TF) en de omgekeerde documentfrequentie (IDF) gebruikt als variabelen om relevantiescores voor elk documentquerypaar te berekenen, wat vervolgens gebruikt voor ranking. Hoewel conceptueel vergelijkbaar met de oudere Classic Similarity algoritme, BM25 neemt zijn wortel in probabilistische informatie ophalen om te verbeteren op het. BM25 biedt ook geavanceerde aanpassingsopties, zoals de gebruiker in staat stellen om te beslissen hoe de relevantiescore schaalt met de term frequentie van overeenkomende termen.

## <a name="how-to-test-bm25-today"></a>Hoe bm25 vandaag te testen

Wanneer u een nieuwe index maakt, u een eigenschap 'gelijkenis' instellen. U moet de versie *2019-05-06-Preview* gebruiken, zoals hieronder weergegeven.

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

Voor services die vóór 15 juli 2020 zijn gemaakt: Als de gelijkenis wordt weggelaten of ingesteld op null, gebruikt de index het oude klassieke gelijkenisalgoritme.

Voor services die na 15 juli 2020 zijn gemaakt: Als de gelijkenis wordt weggelaten of ingesteld op null, gebruikt de index het nieuwe BM25-gelijkenisalgoritme.

U de gelijkeniswaarde ook expliciet instellen als een van de volgende twee waarden: *#Microsoft.Azure.Search.ClassicSimilarity* of *#Microsoft.Azure.Search.BM25Similarity.*


## <a name="bm25-similarity-parameters"></a>BM25 gelijkenisparameters

BM25-gelijkenis voegt twee aanpasbare parameters van de gebruiker toe om de berekende relevantiescore te bepalen:

### <a name="k1"></a>k1 k1

De *parameter K1* regelt de schaalfunctie tussen de termfrequentie van elke overeenkomende termen tot de uiteindelijke relevantiescore van een documentquerypaar.

Een waarde van nul vertegenwoordigt een "binair model", waarbij de bijdrage van één overeenkomende term hetzelfde is voor alle overeenkomende documenten, ongeacht hoe vaak die term in de tekst wordt weergegeven, terwijl een grotere k1-waarde de score laat toenemen naarmate er meer gevallen van dezelfde term vindt u in het document. Azure Cognitive Search gebruikt standaard een waarde van 1,2 voor de parameter K1. Het gebruik van een hogere k1-waarde kan belangrijk zijn in gevallen waarin we verwachten dat meerdere termen deel uitmaken van een zoekopdracht. In die gevallen willen we mogelijk documenten bevoordelen die overeenkomen met veel van de verschillende querytermen die worden doorzocht op documenten die slechts één, meerdere keren overeenkomen. Bijvoorbeeld, bij het opvragen van de index voor documenten met de termen "Apollo Spaceflight", willen we misschien de score van een artikel over de Griekse mythologie, die de term "Apollo" bevat een paar dozijn keer, zonder vermeldingen van "Spaceflight", in vergelijking met een ander artikel dat expliciet noemt zowel "Apollo" en "Spaceflight" een handvol keer alleen. 
 
### <a name="b"></a>b

De *parameter b* bepaalt hoe de lengte van een document de relevantiescore beïnvloedt.

Een waarde van 0,0 betekent dat de lengte van het document geen invloed heeft op de score, terwijl een waarde van 1,0 betekent dat de impact van de termfrequentie op de relevantiescore wordt genormaliseerd door de lengte van het document. De standaardwaarde die wordt gebruikt in Azure Cognitive Search voor de b-parameter is 0,75. Het normaliseren van de term frequentie door de lengte van het document is nuttig in gevallen waarin we langere documenten willen bestraffen. In sommige gevallen bevatten langere documenten (zoals een complete roman) eerder veel irrelevante termen, in vergelijking met veel kortere documenten.

### <a name="setting-k1-and-b-parameters"></a>K1- en b-parameters instellen

Als u de b- of k1-waarden wilt aanpassen, voegt u deze eenvoudig toe als eigenschappen aan het object gelijkenis wanneer u BM25 gebruikt:

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

Het gelijkenisalgoritme kan alleen worden ingesteld op de tijd voor het maken van indexen. Dit betekent dat het gelijkenisalgoritme dat wordt gebruikt, niet kan worden gewijzigd voor bestaande indexen. De *parameters "b"* en *"k1"* kunnen worden gewijzigd bij het bijwerken van een bestaande indexdefinitie die BM25 gebruikt. Als u deze waarden op een bestaande index wijzigt, wordt de index ten minste enkele seconden offline gehaald, waardoor uw indexerings- en queryaanvragen mislukken. Daarom moet u de parameter 'allowIndexDowntime=true' instellen in de queryreeks van uw updateaanvraag:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```


## <a name="see-also"></a>Zie ook  

 [Azure Cognitive Search REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Scoreprofielen toevoegen aan uw index](index-add-scoring-profiles.md)    
 [Index &#40;Azure Cognitive Search REST&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
  [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
