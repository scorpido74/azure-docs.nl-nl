---
title: Tekst splitsing cognitieve Zoek vaardigheid-Azure Search
description: Tekst in segmenten of pagina's met tekst afsplitsen op basis van lengte in een Azure Search verrijkings pijplijn.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: fcde448c664ad85b78d275304dd01a1eedaca006
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183302"
---
#   <a name="text-split-cognitive-skill"></a>Cognitieve vaardigheid van tekst splitsen

De **tekst** gesplitste vaardigheid verbreekt tekst in delen van tekst. U kunt opgeven of u de tekst wilt opsplitsen in zinnen of op pagina's met een bepaalde lengte. Deze vaardigheid is vooral nuttig als er Maxi maal vereisten voor de lengte van de tekst in andere vaardig heden downstream zijn. 

> [!NOTE]
> Deze vaardigheid is niet gebonden aan een Cognitive Services-API en er worden geen kosten in rekening gebracht voor het gebruik ervan. U moet nog steeds [een Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md)om de optie **gratis** resource te overschrijven, waardoor u een klein aantal dagelijkse verrijkingen per dag beperkt.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>Vaardigheids parameters

Para meters zijn hoofdletter gevoelig.

| Parameternaam     | Description |
|--------------------|-------------|
| textSplitMode      | ' Pagina's ' of ' zinnen ' | 
| maximumPageLength | Als textSplitMode is ingesteld op ' pagina's ', verwijst dit naar de maximale pagina lengte, gemeten door `String.Length`. De minimum waarde is 100.  Als de textSplitMode is ingesteld op ' pagina's ', probeert het algoritme de tekst te splitsen in segmenten die de meeste grootte hebben ' maximumPageLength '. In dit geval is het algoritme het beste om de zin op een zin te verkorten, zodat de grootte van het segment iets kleiner is dan ' maximumPageLength '. | 
| defaultLanguageCode   | Beschrijving Een van de volgende taal codes: `da, de, en, es, fi, fr, it, ko, pt`. De standaard waarde is Engels (en). Enkele dingen die u moet overwegen:<ul><li>Als u een language code-CountryCode-indeling doorgeeft, wordt alleen het language code deel van de indeling gebruikt.</li><li>Als de taal zich niet in de vorige lijst bevindt, wordt de tekst door de gesplitste vaardigheid op teken grenzen afgebroken.</li><li>Het is handig om een taal code op te geven om te voor komen dat een woord in de helft wordt geknipt voor niet-beschik bare talen, zoals Chinees, Japans en Koreaans.</li></ul>  |


## <a name="skill-inputs"></a>Vaardigheids invoer

| Parameternaam       | Description      |
|----------------------|------------------|
| text  | De tekst die moet worden gesplitst in subtekenreeks. |
| languageCode  | Beschrijving De taal code voor het document.  |

## <a name="skill-outputs"></a>Vaardigheids uitvoer 

| Parameternaam     | Description |
|--------------------|-------------|
| textItems | Een matrix met subtekenreeksen die zijn geëxtraheerd. |


##  <a name="sample-definition"></a>Voorbeeld definitie

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

##  <a name="sample-output"></a>Voorbeeld uitvoer

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

## <a name="error-cases"></a>Fout cases
Als een taal niet wordt ondersteund, wordt er een waarschuwing gegenereerd en wordt de tekst op teken grenzen gesplitst.

## <a name="see-also"></a>Zie ook

+ [Vooraf gedefinieerde vaardig heden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
