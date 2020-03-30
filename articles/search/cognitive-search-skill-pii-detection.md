---
title: PII Detection cognitieve vaardigheid (preview)
titleSuffix: Azure Cognitive Search
description: Persoonlijk identificeerbare informatie uit tekst in een verrijkingspijplijn in Azure Cognitive Search extraheren en maskeren. Deze vaardigheid is momenteel in openbare preview.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: f21200bc6f5b25f3330f5bb87c0843caa5a84e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298885"
---
#    <a name="pii-detection-cognitive-skill"></a>PII Detectie cognitieve vaardigheid

> [!IMPORTANT] 
> Deze vaardigheid is momenteel in openbare preview. Preview-functionaliteit wordt geleverd zonder overeenkomst op serviceniveau en wordt niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. Er is momenteel geen portal of .NET SDK-ondersteuning.

De **PII Detection-vaardigheid** haalt persoonlijk identificeerbare informatie uit een invoertekst en geeft u de mogelijkheid om deze op verschillende manieren uit die tekst te maskeren. Deze vaardigheid maakt gebruik van de machine learning-modellen van [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) in Cognitive Services.

> [!NOTE]
> Terwijl u het bereik uitbreidt door de frequentie van de verwerking te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureerbare bron voor cognitive services toevoegen.](cognitive-search-attach-cognitive-services.md) Er worden kosten in rekening gebracht bij het aanroepen van API's in Cognitive Services en voor het extraheren van afbeeldingen als onderdeel van de fase van het kraken van documenten in Azure Cognitive Search. Er zijn geen kosten voor tekstextractie uit documenten.
>
> Uitvoering van ingebouwde vaardigheden wordt in rekening gebracht tegen de bestaande [Cognitive Services pay-as-you-go prijs.](https://azure.microsoft.com/pricing/details/cognitive-services/) De prijzen voor imageextractie worden beschreven op de [prijspagina azure cognitive search.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>Gegevenslimieten
De maximale grootte van een record moet 50.000 tekens zijn, gemeten door [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Als u uw gegevens moet opsplitsen voordat u deze naar de vaardigheid verzendt, u overwegen de [vaardigheid Text Split te](cognitive-search-skill-textsplit.md)gebruiken.

## <a name="skill-parameters"></a>Vaardigheidsparameters

Parameters zijn hoofdlettergevoelig en zijn allemaal optioneel.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| standaardLanguageCode |    Taalcode van de invoertekst. Voorlopig wordt `en` alleen steun. |
| minimaalPrecisie | Een waarde tussen 0,0 en 1,0. Als de betrouwbaarheidsscore `piiEntities` (in de uitvoer) lager is dan de ingestelde `minimumPrecision` waarde, wordt de entiteit niet geretourneerd of gemaskeerd. De standaardinstelling is 0,0. |
| maskingMode | Een parameter die verschillende manieren biedt om de gedetecteerde PII in de invoertekst te maskeren. De volgende opties worden ondersteund: <ul><li>`none`(standaard): Dit betekent dat er geen `maskedText` maskering wordt uitgevoerd en dat de uitvoer niet wordt geretourneerd. </li><li> `redact`: Met deze optie worden de gedetecteerde entiteiten uit de invoertekst verwijderd en niet vervangen door iets. Houd er rekening mee dat `piiEntities` in dit geval de verschuiving in de uitvoer ten opzichte van de oorspronkelijke tekst zal zijn en niet de gemaskerde tekst. </li><li> `replace`: Deze optie vervangt de gedetecteerde entiteiten `maskingCharacter` door het teken in de parameter.  Het teken wordt herhaald tot de lengte van de gedetecteerde entiteit, zodat de verschuivingen correct `maskedText`overeenkomen met zowel de invoertekst als de uitvoer .</li></ul> |
| maskerenKarakter | Het teken dat wordt gebruikt om de `maskingMode` tekst te `replace`gemaskeerd als de parameter is ingesteld op . De volgende opties worden `*` ondersteund: `#` `X`(standaard), , . Deze parameter kan `null` `maskingMode` alleen worden `replace`als deze niet is ingesteld op . |


## <a name="skill-inputs"></a>Vaardigheidsingangen

| Invoernaam      | Beschrijving                   |
|---------------|-------------------------------|
| languageCode    | Optioneel. De standaardwaarde is `en`.  |
| tekst          | De tekst om te analyseren.          |

## <a name="skill-outputs"></a>Vaardigheidsuitvoer

| Uitvoernaam      | Beschrijving                   |
|---------------|-------------------------------|
| piiEntiteiten | Een array met complexe typen die de volgende velden bevat: <ul><li>tekst (De werkelijke PII zoals geëxtraheerd)</li> <li>type</li><li>Subtype</li><li>score (Hogere waarde betekent dat het meer kans om een echte entiteit)</li><li>verschuiving (in de invoertekst)</li><li>lengte</li></ul> </br> [Mogelijke types en subTypes zijn hier te vinden.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| gemaskeerdTekst | Als `maskingMode` deze uitvoer is `none`ingesteld op een andere waarde dan , is deze uitvoer het `maskingMode`tekenreeksresultaat van de maskering die wordt uitgevoerd op de invoertekst zoals beschreven door de geselecteerde .  Als `maskingMode` deze `none`is ingesteld op, zal deze uitvoer niet aanwezig zijn. |

##    <a name="sample-definition"></a>Voorbeelddefinitie

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
##    <a name="sample-input"></a>Voorbeeldinvoer

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

Houd er rekening mee dat de verschuivingen die zijn geretourneerd voor entiteiten in de uitvoer van deze vaardigheid rechtstreeks worden geretourneerd vanuit de [Text Analytics-API,](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)wat betekent dat als u ze gebruikt om te indexeren in de oorspronkelijke tekenreeks, u de klasse [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) in .NET moet gebruiken om de juiste inhoud te extraheren.  [Meer details vindt u hier.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-and-warning-cases"></a>Fout- en waarschuwingsaanvragen
Als de taalcode voor het document niet wordt ondersteund, wordt een waarschuwing geretourneerd en worden geen entiteiten geëxtraheerd.
Als uw tekst leeg is, wordt er een waarschuwing weergegeven.
Als uw tekst groter is dan 50.000 tekens, worden alleen de eerste 50.000 tekens geanalyseerd en wordt er een waarschuwing uitgegeven.

Als de vaardigheid een waarschuwing `maskedText` retourneert, kan de uitvoer leeg zijn.  Dit betekent dat als u verwacht dat de output te bestaan voor input in latere vaardigheden, zal het niet werken zoals bedoeld. Houd hier rekening mee bij het schrijven van uw skillset definitie.

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
