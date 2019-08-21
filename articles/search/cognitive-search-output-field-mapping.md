---
title: Verrijkte invoer velden voor cognitieve Zoek opdrachten toewijzen aan uitvoer velden-Azure Search
description: Haal en verrijkende brongegevens velden op en wijs deze toe aan uitvoer velden in een Azure Search index.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 1d1ef7be83601a1ccf09ac52f87d0fcc953c605c
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639195"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Verrijkte velden toewijzen aan een Doorzoek bare index

In dit artikel leert u hoe u verrijkte invoer velden kunt toewijzen aan uitvoer velden in een Doorzoek bare index. Zodra u [een vaardig heden hebt gedefinieerd](cognitive-search-defining-skillset.md), moet u de uitvoer velden toewijzen van alle vaardig heden waarmee waarden rechtstreeks worden bijgedragen aan een bepaald veld in uw zoek index. Veld Toewijzingen zijn vereist voor het verplaatsen van inhoud van verrijkte documenten naar de index.


## <a name="use-outputfieldmappings"></a>OutputFieldMappings gebruiken
Als u velden wilt toewijzen `outputFieldMappings` , voegt u deze toe aan de definitie van de Indexeer functie, zoals hieronder wordt weer gegeven:

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

Het pad in een sourceFieldName kan bestaan uit één element of meerdere elementen. In het bovenstaande ```/document/content/sentiment``` voor beeld vertegenwoordigt een enkele numerieke waarde, terwijl ```/document/content/organizations/*/description``` verschillende beschrijvingen van de organisatie worden aangeduid. Als er meerdere elementen zijn, worden deze ' afgevlakt ' in een matrix die elk van de elementen bevat. In het ```/document/content/organizations/*/description``` voor beeld worden de gegevens in het veld *beschrijvingen* als een vlakke matrix met beschrijvingen weer gegeven voordat de indexering wordt geïndexeerd:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Volgende stappen
Zodra u uw verrijkte velden aan Doorzoek bare velden hebt toegewezen, kunt u de veld kenmerken voor elk van de Doorzoek bare velden instellen [als onderdeel van de definitie van de index](search-what-is-an-index.md).

Zie [veld toewijzingen in azure Search-Indexeer functies](search-indexer-field-mappings.md)voor meer informatie over veld toewijzing.
