---
title: Vaardig heden voor herkenning van entity erkennen-Azure Search
description: Haal verschillende typen entiteiten op uit tekst in een Azure Search cognitieve Zoek pijplijn.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: ad2fef96491c2d1a15ad9ff5f57d2911dfecaa36
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265795"
---
#    <a name="entity-recognition-cognitive-skill"></a>Herkennings vaardigheid van entity erkennen

De kwalificatie voor **entiteits herkenning** extraheert entiteiten van verschillende typen van tekst. Deze vaardigheid maakt gebruik van de machine learning modellen van [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) in cognitive Services.

> [!NOTE]
> Als u het bereik uitbreidt door de verwerkings frequentie te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureer bare Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md). Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in Azure Search. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/)gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met Azure Search prijzen](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Gegevenslimieten
De maximale grootte van een record moet 50.000 tekens zijn, zoals gemeten [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)door. Als u uw gegevens moet opsplitsen voordat u deze naar de sleutel woord groep verstuurt, kunt u overwegen de [Kwalificatie tekst splitsen](cognitive-search-skill-textsplit.md)te gebruiken.

## <a name="skill-parameters"></a>Vaardigheids parameters

Para meters zijn hoofdletter gevoelig en zijn allemaal optioneel.

| Parameternaam     | Description |
|--------------------|-------------|
| categories    | Matrix van categorieën die moeten worden geëxtraheerd.  Mogelijke categorie typen: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, ,`"URL"`. `"Email"` Als er geen categorie wordt opgegeven, worden alle typen geretourneerd.|
|defaultLanguageCode |  De taal code van de invoer tekst. De volgende talen worden ondersteund:`de, en, es, fr, it`|
|minimumPrecision | Vrij. Gereserveerd voor toekomstig gebruik. |
|includeTypelessEntities | Als deze eigenschap is ingesteld op True als de tekst een bekende entiteit bevat, maar niet in een van de ondersteunde categorieën kan worden gecategoriseerd, wordt deze geretourneerd als `"entities"` onderdeel van het complexe uitvoer veld. 
Dit zijn entiteiten die goed bekend zijn, maar niet zijn geclassificeerd als onderdeel van de huidige ondersteunde ' categorieën '. Bijvoorbeeld ' Windows 10 ' is een bekende entiteit (een product), maar ' Products ' is niet in de categorieën die vandaag worden ondersteund. Standaard waarde is`false` |


## <a name="skill-inputs"></a>Vaardigheids invoer

| Naam invoeren      | Description                   |
|---------------|-------------------------------|
| languageCode  | Optioneel. De standaardwaarde is `"en"`.  |
| text          | De tekst die moet worden geanalyseerd.          |

## <a name="skill-outputs"></a>Vaardigheids uitvoer

> [!NOTE]
> Niet alle entiteits categorieën worden ondersteund voor alle talen. Alleen _en_, _es_ `"Quantity"`ondersteunen extractie `"Datetime"` `"Email"` van,,, typen. `"URL"`

| Uitvoer naam     | Description                   |
|---------------|-------------------------------|
| personen      | Een matrix met teken reeksen waarbij elke teken reeks de naam van een persoon vertegenwoordigt. |
| locations  | Een matrix met teken reeksen waarbij elke teken reeks een locatie vertegenwoordigt. |
| organizations  | Een matrix met teken reeksen waarbij elke teken reeks een organisatie vertegenwoordigt. |
| groot  | Een matrix met teken reeksen waarbij elke teken reeks een hoeveelheid vertegenwoordigt. |
| Tijd  | Een matrix met teken reeksen waarbij elke teken reeks een datum/tijd vertegenwoordigt (zoals deze in de tekst) waarde wordt weer gegeven. |
| adres | Een matrix met teken reeksen waarbij elke teken reeks een URL vertegenwoordigt |
| e-mails | Een matrix met teken reeksen waarbij elke teken reeks een e-mail vertegenwoordigt |
| namedEntities | Een matrix met complexe typen die de volgende velden bevat: <ul><li>category</li> <li>waarde (de werkelijke naam van de entiteit)</li><li>offset (de locatie waar deze zich bevindt in de tekst)</li><li>vertrouwen (op dit moment niet gebruikt. Wordt ingesteld op de waarde-1)</li></ul> |
| Rijg | Een matrix met complexe typen met uitgebreide informatie over de entiteiten die zijn geëxtraheerd uit de tekst, met de volgende velden <ul><li> naam (de werkelijke naam van de entiteit. Dit vertegenwoordigt een ' genormaliseerd ' formulier.</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (een koppeling naar de Wikipedia-pagina voor de entiteit)</li><li>bingId</li><li>type (de categorie van de entiteit die wordt herkend)</li><li>subtype (alleen beschikbaar voor bepaalde categorieën geeft dit een gedetailleerdere weer gave van het entiteits type)</li><li> komt overeen (een complexe verzameling die bevat)<ul><li>tekst (de onbewerkte tekst voor de entiteit)</li><li>offset (de locatie waar deze zich bevindt)</li><li>lengte (de lengte van de tekst van de onbewerkte entiteit)</li></ul></li></ul> |

##  <a name="sample-definition"></a>Voorbeeld definitie

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
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
##  <a name="sample-input"></a>Voorbeeld invoer

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

##  <a name="sample-output"></a>Voorbeelduitvoer

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
            "confidence": -1
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


## <a name="error-cases"></a>Fout cases
Als de taal code voor het document niet wordt ondersteund, wordt een fout geretourneerd en worden er geen entiteiten geëxtraheerd.

## <a name="see-also"></a>Zie ook

+ [Vooraf gedefinieerde vaardig heden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
