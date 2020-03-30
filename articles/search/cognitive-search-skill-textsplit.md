---
title: Tekst gesplitst cognitieve vaardigheid
titleSuffix: Azure Cognitive Search
description: Breek tekst op in brokken of pagina's tekst op basis van lengte in een AI-verrijkingspijplijn in Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3f80169808b1e6420f04b786d2bb06bde9c96231
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73479653"
---
# <a name="text-split-cognitive-skill"></a>Tekst gesplitst cognitieve vaardigheid

De **vaardigheid Tekstgesplitst** breekt tekst in stukjes tekst. U opgeven of u de tekst wilt opsplitsen in zinnen of in pagina's van een bepaalde lengte. Deze vaardigheid is vooral handig als er maximale vereisten voor tekstlengte zijn in andere vaardigheden stroomafwaarts. 

> [!NOTE]
> Deze vaardigheid is niet gebonden aan een API voor Cognitive Services en u wordt niet in rekening gebracht voor het gebruik ervan. U moet echter nog steeds [een resource voor Cognitive Services koppelen](cognitive-search-attach-cognitive-services.md)om de optie **Gratis** resource te overschrijven die u beperkt tot een klein aantal dagelijkse verrijkingen per dag.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Vaardigheidsparameters

Parameters zijn hoofdlettergevoelig.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| textSplitMode      | "pagina's" of "zinnen" | 
| maximale paginalengte | Als textSplitMode is ingesteld op 'pagina's', verwijst `String.Length`dit naar de maximale paginalengte zoals gemeten door . De minimumwaarde is 100.  Als de textSplitMode is ingesteld op "pagina's", zal het algoritme proberen om de tekst op te splitsen in brokken die hoogstens "maximumPageLength" in grootte zijn. In dit geval zal het algoritme zijn best doen om de zin op een zinsgrens te breken, zodat de grootte van de chunk iets kleiner kan zijn dan "maximumPageLength". | 
| standaardLanguageCode   | (facultatief) Een van de volgende `da, de, en, es, fi, fr, it, ko, pt`taalcodes: . Standaard is Engels (nl). Weinig dingen om te overwegen:<ul><li>Als u een taalcode-landcode-indeling doorgeeft, wordt alleen het taalcodegedeelte van de indeling gebruikt.</li><li>Als de taal zich niet in de vorige lijst bevindt, doorbreekt de gesplitste vaardigheid de tekst bij tekengrenzen.</li><li>Het verstrekken van een taalcode is handig om te voorkomen dat een woord wordt gehalveerd voor niet-witruimtetalen zoals Chinees, Japans en Koreaans.</li><li>Als u de taal niet kent (d.w.z. u moet de tekst voor invoer splitsen in de [LanguageDetectionSkill),](cognitive-search-skill-language-detection.md)moet de standaardstandaard Engels (en) voldoende zijn. </li></ul>  |


## <a name="skill-inputs"></a>Vaardigheidsingangen

| Parameternaam       | Beschrijving      |
|----------------------|------------------|
| tekst  | De tekst die moet worden opgesplitst in subtekenreeks. |
| languageCode  | (Optioneel) Taalcode voor het document. Als u de taal niet kent (d.w.z. u moet de tekst voor invoer in de [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)splitsen), is het veilig om deze invoer te verwijderen.  |

## <a name="skill-outputs"></a>Vaardigheidsuitvoer 

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| tekstItems | Een array van substrings die werden geëxtraheerd. |


##  <a name="sample-definition"></a>Voorbeelddefinitie

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
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
                "text": "This is a the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
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
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>Foutgevallen
Als een taal niet wordt ondersteund, wordt een waarschuwing gegenereerd en wordt de tekst gesplitst op tekengrenzen.

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
