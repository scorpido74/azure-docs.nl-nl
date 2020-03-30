---
title: Tekst Vertaling cognitieve vaardigheid
titleSuffix: Azure Cognitive Search
description: Evalueert tekst en retourneert voor elke record tekst die is vertaald naar de opgegeven doeltaal in een AI-verrijkingspijplijn in Azure Cognitive Search.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5089174fcfd5a97128c1f789b818243243a5282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460767"
---
#   <a name="text-translation-cognitive-skill"></a>Tekst Vertaling cognitieve vaardigheid

De **vaardigheid Tekstvertaling** evalueert tekst en retourneert voor elke record de vertaalde tekst naar de opgegeven doeltaal. Deze vaardigheid maakt gebruik van de [Translator Text API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) beschikbaar in Cognitive Services.

Deze mogelijkheid is handig als u verwacht dat uw documenten mogelijk niet allemaal in één taal zijn, in welk geval u de tekst normaliseren tot één taal voordat u indexeert voor zoekopdrachten door deze te vertalen.  Het is ook handig voor het lokaliseren van use cases, waarbij u mogelijk kopieën van dezelfde tekst beschikbaar wilt hebben in meerdere talen.

De [Translator Text API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) is een niet-regionale cognitieve service, wat betekent dat uw gegevens niet gegarandeerd in dezelfde regio blijven als uw Azure Cognitive Search of gekoppelde Cognitive Services-bron.

> [!NOTE]
> Terwijl u het bereik uitbreidt door de frequentie van de verwerking te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureerbare bron voor cognitive services toevoegen.](cognitive-search-attach-cognitive-services.md) Er worden kosten in rekening gebracht bij het aanroepen van API's in Cognitive Services en voor het extraheren van afbeeldingen als onderdeel van de fase van het kraken van documenten in Azure Cognitive Search. Er zijn geen kosten voor tekstextractie uit documenten.
>
> Uitvoering van ingebouwde vaardigheden wordt in rekening gebracht tegen de bestaande [Cognitive Services pay-as-you-go prijs.](https://azure.microsoft.com/pricing/details/cognitive-services/) De prijzen voor imageextractie worden beschreven op de [prijspagina azure cognitive search.](https://go.microsoft.com/fwlink/?linkid=2042400)

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.TranslationSkill

## <a name="data-limits"></a>Gegevenslimieten
De maximale grootte van een record moet 50.000 tekens zijn, gemeten door [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Als u uw gegevens moet opsplitsen voordat u deze naar de tekstvertaalvaardigheid stuurt, u overwegen de [vaardigheid Text Split te](cognitive-search-skill-textsplit.md)gebruiken.

## <a name="skill-parameters"></a>Vaardigheidsparameters

Parameters zijn hoofdlettergevoelig.

| Invoer                | Beschrijving |
|---------------------|-------------|
| defaultToLanguageCode | (Vereist) De taalcode om documenten in te vertalen voor documenten die de taal niet expliciet specificeren. <br/> Zie [Volledige lijst met ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| defaultFromLanguageCode | (Optioneel) De taalcode om documenten te vertalen van documenten die niet expliciet de taal opgeven.  Als de standaardFromLanguageCode niet is opgegeven, wordt de automatische taaldetectie van de Translator Text API gebruikt om de taal te bepalen. <br/> Zie [Volledige lijst met ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| voorgesteldUit | (Optioneel) De taalcode om documenten te vertalen van wanneer noch de invoer van FromLanguageCode, noch de standaardparameterFromLanguageCode zijn opgegeven en de automatische taaldetectie mislukt.  Als de voorgesteldeTaal niet is opgegeven, wordt Engels (nl) gebruikt als de voorgesteldeTaal. <br/> Zie [Volledige lijst met ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |

## <a name="skill-inputs"></a>Vaardigheidsingangen

| Invoernaam     | Beschrijving |
|--------------------|-------------|
| tekst | De te vertalen tekst.|
| toLanguageCode    | Een tekenreeks die aangeeft naar welke taal de tekst moet worden vertaald. Als deze invoer niet is opgegeven, wordt de standaardToLanguageCode gebruikt om de tekst te vertalen. <br/>Zie [Volledige lijst met ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|
| fromLanguageCode  | Een tekenreeks die de huidige taal van de tekst aangeeft. Als deze parameter niet is opgegeven, wordt de standaardTaalcode (of automatische taaldetectie als de standaardFromLanguageCode niet wordt opgegeven) gebruikt om de tekst te vertalen. <br/>Zie [Volledige lijst met ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>Vaardigheidsuitvoer

| Uitvoernaam    | Beschrijving |
|--------------------|-------------|
| translatedText | Het tekenreeksresultaat van de tekstvertaling van de vertaaldeFromLanguageCode naar de vertaaldeToLanguageCode.|
| translatedToLanguageCode  | Een tekenreeks die de taalcode aangeeft waarnaar de tekst is vertaald. Handig als u naar meerdere talen vertaalt en wilt kunnen bijhouden welke tekst welke taal is.|
| vertaaldFromLanguageCode    | Een tekenreeks die de taalcode aangeeft waaruit de tekst is vertaald. Handig als u hebt gekozen voor de automatische taaldetectieoptie, omdat deze uitvoer u het resultaat van die detectie geeft.|

##  <a name="sample-definition"></a>Voorbeelddefinitie

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
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
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
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
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Fouten en waarschuwingen
Als u een niet-ondersteunde taalcode opgeeft voor de van of de taal, wordt er een fout gegenereerd en wordt tekst niet vertaald.
Als uw tekst leeg is, wordt er een waarschuwing weergegeven.
Als uw tekst groter is dan 50.000 tekens, worden alleen de eerste 50.000 tekens vertaald en wordt er een waarschuwing gegeven.

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
