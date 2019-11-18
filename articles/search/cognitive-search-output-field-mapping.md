---
title: Invoer toewijzen aan uitvoer velden
titleSuffix: Azure Cognitive Search
description: Haal en verrijkende brongegevens velden op en wijs deze toe aan uitvoer velden in een Azure Cognitive Search-index.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d2d5e717154d16cc5579c1495aff9c1eebf54b17
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132377"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>AI-verrijkte velden toewijzen aan een Doorzoek bare index

In dit artikel leert u hoe u verrijkte invoer velden kunt toewijzen aan uitvoer velden in een Doorzoek bare index. Zodra u [een vaardig heden hebt gedefinieerd](cognitive-search-defining-skillset.md), moet u de uitvoer velden toewijzen van alle vaardig heden waarmee waarden rechtstreeks worden bijgedragen aan een bepaald veld in uw zoek index. Veld Toewijzingen zijn vereist voor het verplaatsen van inhoud van verrijkte documenten naar de index.


## <a name="use-outputfieldmappings"></a>OutputFieldMappings gebruiken
Als u velden wilt toewijzen, voegt u `outputFieldMappings` toe aan de definitie van de Indexeer functie, zoals hieronder wordt weer gegeven:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

De hoofd tekst van de aanvraag is als volgt gestructureerd:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions"
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```
Stel voor elke toewijzing van het uitvoer veld de naam van het verrijkte veld (sourceFieldName) en de naam van het veld in waarnaar wordt verwezen in de index (targetFieldName).

Het pad in een sourceFieldName kan bestaan uit één element of meerdere elementen. In het bovenstaande voor beeld vertegenwoordigt ```/document/content/sentiment``` een enkele numerieke waarde, terwijl ```/document/content/organizations/*/description``` verschillende beschrijvingen van de organisatie vertegenwoordigt. Als er meerdere elementen zijn, worden deze ' afgevlakt ' in een matrix die elk van de elementen bevat. In het ```/document/content/organizations/*/description```e voor beeld worden de gegevens in het veld *beschrijvingen* als een vlakke matrix met beschrijvingen weer gegeven voordat de indexering wordt geïndexeerd:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Volgende stappen
Zodra u uw verrijkte velden aan Doorzoek bare velden hebt toegewezen, kunt u de veld kenmerken voor elk van de Doorzoek bare velden instellen [als onderdeel van de definitie van de index](search-what-is-an-index.md).

Zie [veld toewijzingen in Azure Cognitive Search-Indexeer functies](search-indexer-field-mappings.md)voor meer informatie over het toewijzen van velden.
