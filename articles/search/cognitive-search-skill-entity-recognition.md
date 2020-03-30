---
title: Entiteit herkenning cognitieve vaardigheid
titleSuffix: Azure Cognitive Search
description: Verschillende typen entiteiten uit tekst halen in een verrijkingspijplijn in Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6ef5952b6413563b2c2e16ff2218f709b414fb84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297819"
---
#    <a name="entity-recognition-cognitive-skill"></a>Entiteit herkenning cognitieve vaardigheid

Met de vaardigheid **Entiteitsherkenning** worden entiteiten van verschillende typen uit tekst geëxtraheerd. Deze vaardigheid maakt gebruik van de machine learning-modellen van [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) in Cognitive Services.

> [!NOTE]
> Terwijl u het bereik uitbreidt door de frequentie van de verwerking te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureerbare bron voor cognitive services toevoegen.](cognitive-search-attach-cognitive-services.md) Er worden kosten in rekening gebracht bij het aanroepen van API's in Cognitive Services en voor het extraheren van afbeeldingen als onderdeel van de fase van het kraken van documenten in Azure Cognitive Search. Er zijn geen kosten voor tekstextractie uit documenten.
>
> Uitvoering van ingebouwde vaardigheden wordt in rekening gebracht tegen de bestaande [Cognitive Services pay-as-you-go prijs.](https://azure.microsoft.com/pricing/details/cognitive-services/) De prijzen voor imageextractie worden beschreven op de [prijspagina azure cognitive search.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Gegevenslimieten
De maximale grootte van een record moet 50.000 tekens zijn, gemeten door [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Als u uw gegevens moet opsplitsen voordat u deze naar de trefwoordextractor van de sleutelzin stuurt, u overwegen de [vaardigheid Text Split te](cognitive-search-skill-textsplit.md)gebruiken.

## <a name="skill-parameters"></a>Vaardigheidsparameters

Parameters zijn hoofdlettergevoelig en zijn allemaal optioneel.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| categorieën    | Reeks categorieën die moeten worden geëxtraheerd.  Mogelijke `"Person"`categorietypen: `"Location"` `"Organization"`, `"Quantity"` `"Datetime"`, `"URL"` `"Email"`, , , . Als er geen categorie is opgegeven, worden alle typen geretourneerd.|
|standaardLanguageCode |    Taalcode van de invoertekst. De volgende talen worden `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans`ondersteund: . Niet alle entiteitscategorieën worden voor alle talen ondersteund; zie opmerking hieronder.|
|minimaalPrecisie | Een waarde tussen 0 en 1. Als de betrouwbaarheidsscore `namedEntities` (in de uitvoer) lager is dan deze waarde, wordt de entiteit niet geretourneerd. De standaardwaarde is 0. |
|includeTypelessEntities | Stel `true` in op als u bekende entiteiten wilt herkennen die niet in de huidige categorieën passen. Erkende entiteiten worden geretourneerd `entities` in het complexe uitvoerveld. 'Windows 10' is bijvoorbeeld een bekende entiteit (een product), maar aangezien 'Producten' geen ondersteunde categorie is, wordt deze entiteit opgenomen in het uitvoerveld entiteiten. Standaard is`false` |


## <a name="skill-inputs"></a>Vaardigheidsingangen

| Invoernaam      | Beschrijving                   |
|---------------|-------------------------------|
| languageCode    | Optioneel. De standaardwaarde is `"en"`.  |
| tekst          | De tekst om te analyseren.          |

## <a name="skill-outputs"></a>Vaardigheidsuitvoer

> [!NOTE]
> Niet alle entiteitscategorieën worden voor alle talen ondersteund. De `"Person"` `"Location"`typen `"Organization"` , en entiteitscategorieën worden ondersteund voor de volledige lijst met talen hierboven. Alleen _de_, _en_, _es_, _fr_, `"URL"`en `"Email"` _zh-hans_ ondersteunen extractie van `"Quantity"`, `"Datetime"`, en types. Zie [Taal- en regioondersteuning voor de Text Analytics API voor](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support)meer informatie.  

| Uitvoernaam      | Beschrijving                   |
|---------------|-------------------------------|
| Personen       | Een array met tekenreeksen waarbij elke tekenreeks de naam van een persoon vertegenwoordigt. |
| locaties  | Een array met tekenreeksen waarbij elke tekenreeks een locatie vertegenwoordigt. |
| organizations  | Een array met tekenreeksen waarbij elke tekenreeks een organisatie vertegenwoordigt. |
| Hoeveelheden  | Een array met tekenreeksen waarbij elke tekenreeks een hoeveelheid vertegenwoordigt. |
| datumTimes  | Een reeks tekenreeksen waarbij elke tekenreeks een datumtijdwaarde vertegenwoordigt (zoals deze in de tekst wordt weergegeven). |
| Urls | Een array met tekenreeksen waarbij elke tekenreeks een URL vertegenwoordigt |
| E-mails | Een reeks tekenreeksen waarbij elke tekenreeks een e-mail vertegenwoordigt |
| namedEnentiteiten | Een array met complexe typen die de volgende velden bevat: <ul><li>category</li> <li>waarde (De werkelijke entiteitsnaam)</li><li>verschuiving (de locatie waar deze in de tekst is gevonden)</li><li>vertrouwen (Hogere waarde betekent dat het meer is om een echte entiteit te zijn)</li></ul> |
| Entiteiten | Een array met complexe typen die uitgebreide informatie bevat over de entiteiten die uit tekst zijn geëxtraheerd, met de volgende velden <ul><li> naam (de werkelijke entiteitsnaam. Dit vertegenwoordigt een "genormaliseerd" formulier)</li><li> wikipediaId</li><li>wikipediaTaal</li><li>wikipediaUrl (een link naar Wikipedia-pagina voor de entiteit)</li><li>bingId</li><li>type (de categorie van de entiteit die is erkend)</li><li>subType (alleen beschikbaar voor bepaalde categorieën, dit geeft een meer gedetailleerde weergave van het entiteitstype)</li><li> overeenkomsten (een complexe verzameling die bevat)<ul><li>tekst (de ruwe tekst voor de entiteit)</li><li>offset (de locatie waar deze is gevonden)</li><li>lengte (de lengte van de tekst van de ruwe entiteit)</li></ul></li></ul> |

##    <a name="sample-definition"></a>Voorbeelddefinitie

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
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
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
      }
    ]
  }
```
##    <a name="sample-input"></a>Voorbeeldinvoer

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": 0.98
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```

Houd er rekening mee dat de verschuivingen die zijn geretourneerd voor entiteiten in de uitvoer van deze vaardigheid rechtstreeks worden geretourneerd vanuit de [Text Analytics-API,](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)wat betekent dat als u ze gebruikt om te indexeren in de oorspronkelijke tekenreeks, u de klasse [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) in .NET moet gebruiken om de juiste inhoud te extraheren.  [Meer details vindt u hier.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-cases"></a>Foutgevallen
Als de taalcode voor het document niet wordt ondersteund, wordt een fout geretourneerd en worden er geen entiteiten geëxtraheerd.

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
