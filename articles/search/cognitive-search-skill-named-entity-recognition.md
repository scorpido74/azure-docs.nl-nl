---
title: Benoemde entiteitsherkenningsvaardigheid
titleSuffix: Azure Cognitive Search
description: Haal benoemde entiteiten voor persoon, locatie en organisatie uit tekst in een AI-verrijkingspijplijn in Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 127155e492b556ce1ce02b67cf0b0846b99ebcd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791937"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Benoemde entiteitsherkenningsvaardigheid

De **vaardigheid Named Entity Recognition** haalt benoemde entiteiten uit tekst. Beschikbare entiteiten omvatten `person` `location` de `organization`typen en .

> [!IMPORTANT]
> Benoemde entiteitsherkenningsvaardigheid wordt nu stopgezet, vervangen door [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). Ondersteuning gestopt op 15 februari 2019 en de API is op 2 mei 2019 uit het product verwijderd. Volg de aanbevelingen in [Deprecated cognitieve zoekvaardigheden](cognitive-search-skill-deprecated.md) om te migreren naar een ondersteunde vaardigheid.

> [!NOTE]
> Terwijl u het bereik uitbreidt door de frequentie van de verwerking te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureerbare bron voor cognitive services toevoegen.](cognitive-search-attach-cognitive-services.md) Er worden kosten in rekening gebracht bij het aanroepen van API's in Cognitive Services en voor het extraheren van afbeeldingen als onderdeel van de fase van het kraken van documenten in Azure Cognitive Search. Er zijn geen kosten voor tekstextractie uit documenten.
>
> Uitvoering van ingebouwde vaardigheden wordt in rekening gebracht tegen de bestaande [Cognitive Services pay-as-you-go prijs.](https://azure.microsoft.com/pricing/details/cognitive-services/) De prijzen voor imageextractie worden beschreven op de [prijspagina azure cognitive search.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>Gegevenslimieten
De maximale grootte van een record moet 50.000 tekens zijn, gemeten door [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Als u uw gegevens moet opsplitsen voordat u deze naar de trefwoordextractor van de sleutelzin stuurt, u overwegen de [vaardigheid Text Split te](cognitive-search-skill-textsplit.md)gebruiken.

## <a name="skill-parameters"></a>Vaardigheidsparameters

Parameters zijn hoofdlettergevoelig.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| categorieën    | Reeks categorieën die moeten worden geëxtraheerd.  Mogelijke `"Person"`categorietypen: `"Location"` `"Organization"`, , . Als er geen categorie is opgegeven, worden alle typen geretourneerd.|
|standaardLanguageCode |  Taalcode van de invoertekst. De volgende talen worden ondersteund:`de, en, es, fr, it`|
| minimaalPrecisie  | Een getal tussen 0 en 1. Als de precisie lager is dan deze waarde, wordt de entiteit niet geretourneerd. De standaardwaarde is 0.|

## <a name="skill-inputs"></a>Vaardigheidsingangen

| Invoernaam      | Beschrijving                   |
|---------------|-------------------------------|
| languageCode  | Optioneel. De standaardwaarde is `"en"`.  |
| tekst          | De tekst om te analyseren.          |

## <a name="skill-outputs"></a>Vaardigheidsuitvoer

| Uitvoernaam     | Beschrijving                   |
|---------------|-------------------------------|
| Personen      | Een array met tekenreeksen waarbij elke tekenreeks de naam van een persoon vertegenwoordigt. |
| locaties  | Een array met tekenreeksen waarbij elke tekenreeks een locatie vertegenwoordigt. |
| organizations  | Een array met tekenreeksen waarbij elke tekenreeks een organisatie vertegenwoordigt. |
| Entiteiten | Een scala aan complexe typen. Elk complex type bevat de volgende velden: <ul><li>categorie`"person"`( `"organization"`, `"location"`of )</li> <li>waarde (de werkelijke entiteitsnaam)</li><li>verschuiving (de locatie waar deze in de tekst is gevonden)</li><li>vertrouwen (een waarde tussen 0 en 1 die het vertrouwen vertegenwoordigt dat de waarde een werkelijke entiteit is)</li></ul> |

##  <a name="sample-definition"></a>Voorbeelddefinitie

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```
##  <a name="sample-input"></a>Voorbeeldinvoer

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia… Ana Smith is provided as a reference.",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Foutgevallen
Als de taalcode voor het document niet wordt ondersteund, wordt een fout geretourneerd en worden er geen entiteiten geëxtraheerd.

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
+ [Entiteitserkenningsvaardigheid](cognitive-search-skill-entity-recognition.md)
