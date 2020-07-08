---
title: PERSOONLIJKE detectie cognitieve vaardigheid (preview-versie)
titleSuffix: Azure Cognitive Search
description: U haalt persoonlijke gegevens op uit tekst in een verrijkings pijplijn in azure Cognitive Search. Deze vaardigheid is momenteel beschikbaar als open bare preview.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: bec993c2b59aa03195b78a02668baf3f5fac6695
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85080753"
---
#    <a name="pii-detection-cognitive-skill"></a>PERSOONLIJKE detectie cognitieve vaardigheid

> [!IMPORTANT] 
> Deze vaardigheid is momenteel beschikbaar als open bare preview. Deze previewfunctie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. Er is momenteel geen portal-of .NET SDK-ondersteuning.

De persoonlijke **detectie** -vaardigheid extraheert persoons gegevens uit een invoer tekst en biedt u de mogelijkheid om deze op verschillende manieren te maskeren in de tekst. Deze vaardigheid maakt gebruik van de machine learning modellen van [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) in cognitive Services.

> [!NOTE]
> Als u het bereik uitbreidt door de verwerkings frequentie te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureer bare Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md). Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in azure Cognitive Search. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/)gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met prijzen voor Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Micro soft. skills. Text. PIIDetectionSkill

## <a name="data-limits"></a>Gegevenslimieten
De maximale grootte van een record moet 50.000 tekens zijn, zoals gemeten door [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length) . Als u uw gegevens moet opsplitsen voordat u deze naar de vaardigheid verzendt, kunt u overwegen de [Kwalificatie tekst splitsen](cognitive-search-skill-textsplit.md)te gebruiken.

## <a name="skill-parameters"></a>Vaardigheids parameters

Para meters zijn hoofdletter gevoelig en zijn optioneel.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| `defaultLanguageCode` |    De taal code van de invoer tekst. Alleen voor nu `en` wordt alleen ondersteund. |
| `minimumPrecision` | Een waarde tussen 0,0 en 1,0. Als de betrouwbaarheids Score (in de `piiEntities` uitvoer) lager is dan de ingestelde `minimumPrecision` waarde, wordt de entiteit niet geretourneerd of gemaskeerd. De standaard waarde is 0,0. |
| `maskingMode` | Een para meter die verschillende manieren biedt om de gedetecteerde PII in de invoer tekst te maskeren. De volgende opties worden ondersteund: <ul><li>`none`(standaard): Dit betekent dat er geen maskering wordt uitgevoerd en dat de `maskedText` uitvoer niet wordt geretourneerd. </li><li> `redact`: Met deze optie worden de gedetecteerde entiteiten uit de invoer tekst verwijderd en worden ze niet vervangen door iets anders. In dit geval is de verschuiving in de `piiEntities` uitvoer gerelateerd aan de oorspronkelijke tekst en niet de gemaskeerde tekst. </li><li> `replace`: Met deze optie worden de gedetecteerde entiteiten vervangen door het teken dat is opgegeven in de `maskingCharacter` para meter.  Het teken wordt herhaald tot de lengte van de gedetecteerde entiteit, zodat de verschuivingen goed overeenkomen met zowel de invoer tekst als de uitvoer `maskedText` .</li></ul> |
| `maskingCharacter` | Het teken dat wordt gebruikt om de tekst te maskeren als de `maskingMode` para meter is ingesteld op `replace` . De volgende opties worden ondersteund: `*` (standaard), `#` , `X` . Deze para meter kan alleen `null` worden `maskingMode` opgegeven als niet is ingesteld op `replace` . |


## <a name="skill-inputs"></a>Vaardigheids invoer

| Invoer naam      | Description                   |
|---------------|-------------------------------|
| `languageCode`    | Optioneel. De standaardwaarde is `en`.  |
| `text`          | De tekst die moet worden geanalyseerd.          |

## <a name="skill-outputs"></a>Vaardigheids uitvoer

| Uitvoer naam      | Description                   |
|---------------|-------------------------------|
| `piiEntities` | Een matrix met complexe typen die de volgende velden bevat: <ul><li>tekst (de werkelijke PII als geëxtraheerd)</li> <li>type</li><li>subType</li><li>Score (hogere waarde betekent dat er waarschijnlijk een echte entiteit is)</li><li>offset (in de invoer tekst)</li><li>lengte</li></ul> </br> [Mogelijke typen en subtypen kunt u hier vinden.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| `maskedText` | Als `maskingMode` is ingesteld op een andere waarde dan `none` , wordt deze uitvoer het teken reeks resultaat van de maskering die wordt uitgevoerd op de invoer tekst, zoals wordt beschreven door de geselecteerde `maskingMode` .  Als `maskingMode` is ingesteld op `none` , is deze uitvoer niet aanwezig. |

##    <a name="sample-definition"></a>Voorbeeld definitie

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
      }
    ]
  }
```
##    <a name="sample-input"></a>Voorbeeld invoer

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
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
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

Houd er rekening mee dat de verplaatsingen die worden geretourneerd voor entiteiten in de uitvoer van deze vaardigheid rechtstreeks worden geretourneerd uit de [Text Analytics-API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). Dit betekent dat als u deze gebruikt om te indexeren in de oorspronkelijke teken reeks, u de [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) -klasse in .net moet gebruiken om de juiste inhoud te extra heren.  [Meer informatie vindt u hier.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-and-warning-cases"></a>Fout-en waarschuwings cases
Als de taal code voor het document niet wordt ondersteund, wordt een waarschuwing geretourneerd en worden er geen entiteiten geëxtraheerd.
Als uw tekst leeg is, wordt er een waarschuwing gegenereerd.
Als uw tekst groter is dan 50.000 tekens, worden alleen de eerste 50.000 tekens geanalyseerd en wordt er een waarschuwing gegeven.

Als de vaardigheid een waarschuwing retourneert, kan de uitvoer `maskedText` leeg zijn.  Dit betekent dat als u verwacht dat de uitvoer bestaat voor invoer in latere vaardig heden, deze niet werkt zoals bedoeld. Houd dit in acht wanneer u uw definitie van uw vaardig heden schrijft.

## <a name="see-also"></a>Zie tevens

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
