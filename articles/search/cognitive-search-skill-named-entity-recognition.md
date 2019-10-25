---
title: Herkennings vaardigheid van benoemde entiteiten
titleSuffix: Azure Cognitive Search
description: Haalt benoemde entiteiten voor persoon, locatie en organisatie uit tekst op in een AI-verrijkings pijplijn in azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 127155e492b556ce1ce02b67cf0b0846b99ebcd4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791937"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Herkennings vaardigheid van benoemde entiteiten

De **benoemde entiteit herkennings** kwalificatie haalt benoemde entiteiten uit tekst op. Beschik bare entiteiten zijn de typen `person`, `location` en `organization`.

> [!IMPORTANT]
> Kwalificatie voor erkenning van benoemde entiteiten wordt nu door [micro soft. skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)vervangen. Ondersteuning gestopt op 15 februari 2019 en de API is verwijderd uit het product op 2 mei 2019. Volg de aanbevelingen in [afgeschafte vaardig heden voor cognitieve zoek acties](cognitive-search-skill-deprecated.md) om naar een ondersteunde vaardigheid te migreren.

> [!NOTE]
> Als u het bereik uitbreidt door de verwerkings frequentie te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureer bare Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md). Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in azure Cognitive Search. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/)gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met prijzen voor Azure Cognitive Search](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Micro soft. skills. Text. NamedEntityRecognitionSkill

## <a name="data-limits"></a>Gegevenslimieten
De maximale grootte van een record moet 50.000 tekens zijn, zoals gemeten door [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Als u uw gegevens moet opsplitsen voordat u deze naar de sleutel woord groep verstuurt, kunt u overwegen de [Kwalificatie tekst splitsen](cognitive-search-skill-textsplit.md)te gebruiken.

## <a name="skill-parameters"></a>Vaardigheids parameters

Para meters zijn hoofdletter gevoelig.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| categorieën    | Matrix van categorieën die moeten worden geëxtraheerd.  Mogelijke categorie typen: `"Person"`, `"Location"``"Organization"`. Als er geen categorie wordt opgegeven, worden alle typen geretourneerd.|
|defaultLanguageCode |  De taal code van de invoer tekst. De volgende talen worden ondersteund: `de, en, es, fr, it`|
| minimumPrecision  | Een getal tussen 0 en 1. Als de precisie lager is dan deze waarde, wordt de entiteit niet geretourneerd. De standaard waarde is 0.|

## <a name="skill-inputs"></a>Vaardigheids invoer

| Invoer naam      | Beschrijving                   |
|---------------|-------------------------------|
| languageCode  | Optioneel. De standaardwaarde is `"en"`.  |
| tekst          | De tekst die moet worden geanalyseerd.          |

## <a name="skill-outputs"></a>Vaardigheids uitvoer

| Uitvoer naam     | Beschrijving                   |
|---------------|-------------------------------|
| personen      | Een matrix met teken reeksen waarbij elke teken reeks de naam van een persoon vertegenwoordigt. |
| locaties  | Een matrix met teken reeksen waarbij elke teken reeks een locatie vertegenwoordigt. |
| organizations  | Een matrix met teken reeksen waarbij elke teken reeks een organisatie vertegenwoordigt. |
| Rijg | Een matrix met complexe typen. Elk complex type bevat de volgende velden: <ul><li>categorie (`"person"`, `"organization"`of `"location"`)</li> <li>waarde (de werkelijke naam van de entiteit)</li><li>offset (de locatie waar deze zich bevindt in de tekst)</li><li>betrouw baarheid (een waarde tussen 0 en 1 die aangeeft dat het vertrouwen van de waarde een werkelijke entiteit is)</li></ul> |

##  <a name="sample-definition"></a>Voorbeeld definitie

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
##  <a name="sample-input"></a>Voorbeeld invoer

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


## <a name="error-cases"></a>Fout cases
Als de taal code voor het document niet wordt ondersteund, wordt een fout geretourneerd en worden er geen entiteiten geëxtraheerd.

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardig heden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Vaardigheid van entiteits herkenning](cognitive-search-skill-entity-recognition.md)
