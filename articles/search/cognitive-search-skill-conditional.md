---
title: Voorwaardelijke cognitieve vaardigheid
titleSuffix: Azure Cognitive Search
description: De voorwaardelijke vaardigheid in Azure Cognitive Search maakt het mogelijk om standaardwaarden te filteren, standaardwaarden te maken en waarden samen te voegen in een skillset-definitie.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b5f1fc7f877854dd06fbbe09ff82e47208fa12d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792047"
---
# <a name="conditional-cognitive-skill"></a>Voorwaardelijke cognitieve vaardigheid

Met **de voorwaardelijke** vaardigheid kunnen Azure Cognitive Search-scenario's worden uitgevoerd waarvoor een Booleaanse bewerking nodig is om de gegevens te bepalen die aan een uitvoer moeten worden toegewezen. Deze scenario's omvatten filteren, het toewijzen van een standaardwaarde en het samenvoegen van gegevens op basis van een voorwaarde.

De volgende pseudocode laat zien wat de voorwaardelijke vaardigheid bereikt:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Deze vaardigheid is niet gebonden aan een Azure Cognitive Services API en u wordt niet in rekening gebracht voor het gebruik ervan. U moet echter nog steeds [een resource voor Cognitive Services koppelen](cognitive-search-attach-cognitive-services.md) om de resourceoptie 'Gratis' te overschrijven die u beperkt tot een klein aantal verrijkingen per dag.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Geëvalueerde velden

Deze vaardigheid is speciaal omdat de ingangen worden geëvalueerd velden.

De volgende items zijn geldige waarden van een expressie:

-   Annotatiepaden (paden in expressies moeten worden afgebakend door '$(" en ')')
 <br/>
    Voorbeelden:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literals (tekenreeksen, getallen, waar, onwaar, null) <br/>
    Voorbeelden:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Expressies die vergelijkingsoperatoren gebruiken (==, !=, >=, >, <=, <) <br/>
    Voorbeelden:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Expressies die Booleaanse operatoren gebruiken (&&, ||, !, ^) <br/>
    Voorbeelden:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Expressies die numerieke operatoren \*gebruiken (+, -, , /, %) <br/>
    Voorbeelden: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Omdat de voorwaardelijke vaardigheid evaluatie ondersteunt, u deze gebruiken in scenario's voor kleine transformatie. Zie bijvoorbeeld [vaardigheidsdefinitie 4](#transformation-example).

## <a name="skill-inputs"></a>Vaardigheidsingangen
De invoer is hoofdlettergevoelig.

| Invoer   | Beschrijving |
|-------------|-------------|
| Voorwaarde   | Deze invoer is een [geëvalueerd veld](#evaluated-fields) dat de voorwaarde vertegenwoordigt om te evalueren. Deze voorwaarde moet worden geëvalueerd op een Booleaanse waarde *(waar* of *onwaar).*   <br/>  Voorbeelden: <br/> "= waar" <br/> "= $(/document/taal) =='fr'" <br/> "= $(/document/pages/\*/taal) == $(/document/expectedLanguage)" <br/> |
| wanneerTrue    | Deze invoer is een [geëvalueerd veld](#evaluated-fields) dat de waarde vertegenwoordigt die moet worden retournerd als de voorwaarde wordt geëvalueerd op *true*. Constanten tekenreeksen moeten worden geretourneerd in enkele aanhalingstekens (' en '). <br/>Voorbeeldwaarden: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entiteiten/\*)" <br/> |
| wanneerFalse   | Deze invoer is een [geëvalueerd veld](#evaluated-fields) dat de waarde vertegenwoordigt die moet worden geretourneerd als de voorwaarde wordt geëvalueerd op *false*. <br/>Voorbeeldwaarden: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entiteiten/\*)" <br/>

## <a name="skill-outputs"></a>Vaardigheidsuitvoer
Er is een enkele output die simpelweg 'output' wordt genoemd. Het retourneert de waarde *wanneerFalse* als de voorwaarde vals is of *wanneerTrue* als de voorwaarde waar is.

## <a name="examples"></a>Voorbeelden

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Voorbeeldvaardigheiddefinitie 1: Documenten filteren om alleen Franse documenten terug te sturen

De volgende uitvoer retourneert een reeks zinnen ("/document/frenchZinnen") als de taal van het document Frans is. Als de taal niet Frans is, wordt de waarde ingesteld op *null*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Als "/document/frenchSentences" wordt gebruikt als de *context* van een andere vaardigheid, wordt die vaardigheid alleen uitgevoerd als "/document/frenchSentences" niet is ingesteld op *null.*


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Voorbeeldvaardigheiddefinitie 2: Een standaardwaarde instellen voor een waarde die niet bestaat

Met de volgende uitvoer wordt een annotatie gemaakt ("/document/languageWithDefault") die is ingesteld op de taal van het document of op 'es' als de taal niet is ingesteld.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Voorbeeldvaardigheiddefinitie 3: Waarden uit twee velden samenvoegen tot één

In dit voorbeeld hebben sommige zinnen een *eigenschap frenchSentiment.* Wanneer de *frenchSentiment* eigenschap is null, willen we de *EnglishSentiment* waarde te gebruiken. We wijzen de uitvoer toe aan een lid dat *sentiment* wordt genoemd ("/document/sentiment/*/sentiment").

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>Voorbeeld van transformatie
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Voorbeeldvaardigheiddefinitie 4: Gegevenstransformatie op één veld

In dit voorbeeld ontvangen we een *gevoel* dat tussen 0 en 1 ligt. We willen het transformeren om tussen -1 en 1 te zijn. We kunnen de voorwaardelijke vaardigheid gebruiken om deze kleine transformatie te doen.

In dit voorbeeld gebruiken we het voorwaardelijke aspect van de vaardigheid niet omdat de voorwaarde altijd *waar*is.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>Bijzondere overwegingen
Sommige parameters worden geëvalueerd, dus je moet vooral voorzichtig zijn om het gedocumenteerde patroon te volgen. Expressies moeten beginnen met een gelijk teken. Een pad moet worden afgebakend door "$(" en ")". Zorg ervoor dat u tekenreeksen in enkele aanhalingstekens plaatst. Dat helpt de evaluator onderscheid te maken tussen tekenreeksen en werkelijke paden en operatoren. Zorg er ook voor dat u witte ruimte rond operatoren plaatst (bijvoorbeeld een "*" in een pad betekent iets anders dan vermenigvuldigen).


## <a name="next-steps"></a>Volgende stappen

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
