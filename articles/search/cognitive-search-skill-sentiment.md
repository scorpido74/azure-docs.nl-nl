---
title: Sentiment cognitieve vaardigheid
titleSuffix: Azure Cognitive Search
description: Haal een positief-negatieve sentimentscore uit tekst in een AI-verrijkingspijplijn in Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3aab703b9c5ffcb5f3280060417ce32fcec2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791900"
---
# <a name="sentiment-cognitive-skill"></a>Sentiment cognitieve vaardigheid

De **sentimentvaardigheid** evalueert ongestructureerde tekst langs een positief-negatief continuüm en geeft voor elke record een numerieke score tussen 0 en 1. Scores dicht bij 1 geven een positief sentiment aan en scores dicht bij 0 geven een negatief sentiment aan. Deze vaardigheid maakt gebruik van de machine learning-modellen van [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) in Cognitive Services.

> [!NOTE]
> Terwijl u het bereik uitbreidt door de frequentie van de verwerking te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureerbare bron voor cognitive services toevoegen.](cognitive-search-attach-cognitive-services.md) Er worden kosten in rekening gebracht bij het aanroepen van API's in Cognitive Services en voor het extraheren van afbeeldingen als onderdeel van de fase van het kraken van documenten in Azure Cognitive Search. Er zijn geen kosten voor tekstextractie uit documenten.
>
> Uitvoering van ingebouwde vaardigheden wordt in rekening gebracht tegen de bestaande [Cognitive Services pay-as-you-go prijs.](https://azure.microsoft.com/pricing/details/cognitive-services/) De prijzen voor imageextractie worden beschreven op de [prijspagina azure cognitive search.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Gegevenslimieten
De maximale grootte van een record moet 5000 tekens zijn, gemeten door [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Als u uw gegevens moet opsplitsen voordat u deze naar de sentimentanalyzer stuurt, gebruikt u de [vaardigheid Text Split](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Vaardigheidsparameters

Parameters zijn hoofdlettergevoelig.

| Parameternaam |                      |
|----------------|----------------------|
| standaardLanguageCode | (facultatief) De taalcode die moet worden toegepast op documenten die geen expliciet taal opgeven. <br/> Zie [Volledige lijst met ondersteunde talen](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Vaardigheidsingangen 

| Invoernaam | Beschrijving |
|--------------------|-------------|
| tekst | De te analyseren tekst.|
| languageCode  |  (Optioneel) Een tekenreeks die de taal van de records aangeeft. Als deze parameter niet is opgegeven, is de standaardwaarde 'nl'. <br/>Zie [Volledige lijst met ondersteunde talen](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Vaardigheidsuitvoer

| Uitvoernaam | Beschrijving |
|--------------------|-------------|
| Score | Een waarde tussen 0 en 1 die het sentiment van de geanalyseerde tekst vertegenwoordigt. Waarden dicht bij 0 hebben een negatief sentiment, dicht bij 0,5 hebben een neutraal sentiment en waarden dicht bij 1 hebben een positief sentiment.|


##  <a name="sample-definition"></a>Voorbeelddefinitie

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
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
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
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
            "data": {
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>Opmerkingen
Als deze leeg is, wordt een gevoelsscore niet geretourneerd voor die records.

## <a name="error-cases"></a>Foutgevallen
Als een taal niet wordt ondersteund, wordt er een fout gegenereerd en wordt er geen gevoelsscore geretourneerd.

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
