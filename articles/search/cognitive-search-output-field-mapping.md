---
title: Invoer toewijzen aan uitvoervelden
titleSuffix: Azure Cognitive Search
description: Brongegevensvelden extraheren en verrijken en toewijzen aan uitvoervelden in een Azure Cognitive Search-index.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f0537af684632a08a39e3e681900d62238365073
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74280970"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>AI-verrijkte velden toewijzen aan een doorzoekbare index

In dit artikel leert u hoe u verrijkte invoervelden in een doorzoekbare index in een doorzoekbare index toewijzen. Zodra u [een skillset](cognitive-search-defining-skillset.md)hebt gedefinieerd, moet u de uitvoervelden van elke vaardigheid in kaart brengen die rechtstreeks waarden bijdraagt aan een bepaald veld in uw zoekindex. 

Uitvoerveldtoewijzingen zijn vereist voor het verplaatsen van inhoud van verrijkte documenten naar de index.  Het verrijkte document is echt een boom met informatie, en hoewel er ondersteuning is voor complexe typen in de index, wilt u soms de informatie van de verrijkte boom omzetten in een eenvoudiger type (bijvoorbeeld een array van tekenreeksen). Met veldtoewijzingen voor uitvoerkunt u gegevensvormtransformaties uitvoeren door informatie af te vlakken.

## <a name="use-outputfieldmappings"></a>UitvoerVeldtoewijzingen gebruiken
Als u velden `outputFieldMappings` wilt toewijzen, voegt u toe aan de definitie van indexer zoals hieronder weergegeven:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

De tekst van het verzoek is als volgt gestructureerd:

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

Stel voor elke toewijzing van uitvoerveldde locatie van de gegevens in de verrijkte documentstructuur (sourceFieldName) en de naam van het veld zoals verwezen in de index (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Afvlakkenvan informatie uit complexe typen 

Het pad in een sourceFieldName kan één element of meerdere elementen vertegenwoordigen. In het bovenstaande ```/document/content/sentiment``` voorbeeld staat een ```/document/content/organizations/*/description``` enkele numerieke waarde, terwijl deze verschillende organisatiebeschrijvingen vertegenwoordigt. 

In gevallen waarin er verschillende elementen zijn, worden ze "afgevlakt" in een array die elk van de elementen bevat. 

Meer concreet, bijvoorbeeld, de ```/document/content/organizations/*/description``` gegevens in het *beschrijvingen* veld zou eruit zien als een platte array van beschrijvingen voordat het wordt geïndexeerd:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Dit is een belangrijk beginsel, dus we zullen een ander voorbeeld geven. Stel je voor dat je een scala aan complexe types hebt als onderdeel van de verrijkingsboom. Stel dat er een lid is dat customTities heet en een array met complexe typen heeft, zoals hieronder beschreven.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

Laten we aannemen dat uw index een veld heeft dat 'ziekten' van het type Collection (Edm.String) wordt genoemd, waar u elk van de namen van de entiteiten wilt opslaan. 

Dit kan eenvoudig met behulp\*van het " " symbool, als volgt:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Deze bewerking zal eenvoudig elk van de namen van de elementen van de aangepaste entiteiten "platmaken" in één reeks tekenreeksen als deze:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Volgende stappen
Nadat u uw verrijkte velden hebt toegewezen aan doorzoekbare velden, u de veldkenmerken voor elk van de doorzoekbare velden instellen [als onderdeel van de indexdefinitie](search-what-is-an-index.md).

Zie [Veldtoewijzingen in Azure Cognitive Search-indexeerders](search-indexer-field-mappings.md)voor meer informatie over veldtoewijzingen.
