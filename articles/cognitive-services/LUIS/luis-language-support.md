---
title: Taal ondersteuning-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS heeft tal van functies in de service. Niet alle functies zijn op de dezelfde taalpariteit. Zorg ervoor dat u geïnteresseerd bent in functies worden ondersteund in de taal-cultuur die u ontwikkelt. Een LUIS-app is specifiek voor cultuur en kan niet worden gewijzigd nadat deze is ingesteld.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: bd1e665114fff4d5b7b0b2dca267207bdeebab56
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949552"
---
# <a name="language-and-region-support-for-luis"></a>Ondersteuning voor LUIS taal en regio

LUIS heeft tal van functies in de service. Niet alle functies zijn op de dezelfde taalpariteit. Zorg ervoor dat u geïnteresseerd bent in functies worden ondersteund in de taal-cultuur die u ontwikkelt. Een LUIS-app is specifiek voor cultuur en kan niet worden gewijzigd nadat deze is ingesteld.

## <a name="multi-language-luis-apps"></a>Apps voor meerdere talen LUIS

Als u een clienttoepassing voor meerdere talen LUIS, zoals een chatbot nodig hebt, hebt u een aantal opties. Als er LUIS biedt ondersteuning voor alle talen, ontwikkelt u een LUIS-app voor elke taal. Elke LUIS-app heeft een unieke app-ID en de eindpunt-Logboeken. Als u nodig hebt voor taalbegrip voor een taal LUIS niet ondersteunt, kunt u [Microsoft Translator API](../Translator/translator-info-overview.md) om te zetten de utterance in een ondersteunde taal, de utterance naar het eindpunt LUIS verzenden en ontvangen de resulterende scores.

## <a name="languages-supported"></a>Ondersteunde talen

LUIS begrijpt uitingen in de volgende talen:

| Taal |Landinstelling  |  Vooraf gedefinieerde domein | Vooraf gemaakte entiteiten | Aanbevelingen voor woordgroepen lijst | **[Tekstanalyse](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Sentiment en<br>Trefwoorden)|
|--|--|:--:|:--:|:--:|:--:|
| Amerikaans-Engels |`en-US` | ✔ | ✔  |✔|✔|
| *[Chinees](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Nederlands |`nl-NL` |✔|  -   |-|✔|
| Frans (Frankrijk) |`fr-FR` |✔| ✔ |✔ |✔|
| Frans (Canada) |`fr-CA` |-|   -   |-|✔|
| Duits |`de-DE` |✔| ✔ |✔ |✔|
| Hindi | `hi-IN`|-|-|-|-|
| Italiaans |`it-IT` |✔| ✔ |✔|✔|
| *[Japans](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Alleen sleuteluitdrukkingen|
| Koreaans |`ko-KR` |✔|   -   |-|Alleen sleuteluitdrukkingen|
| Portugees (Brazilië) |`pt-BR` |✔| ✔ |✔ |niet alle onderliggende culturen|
| Spaans (Spanje) |`es-ES` |✔| ✔ |✔|✔|
| Spaans (Mexico)|`es-MX` |-|  -   |✔|✔|
| Turks | `tr-TR` |✔|-|-|Alleen sentiment|


Taalondersteuning varieert voor [vooraf gemaakte entiteiten](luis-reference-prebuilt-entities.md) en [vooraf gemaakte domeinen](luis-reference-prebuilt-domains.md).

### <a name="chinese-support-notes"></a>\* Opmerkingen bij de ondersteuning voor Chinees

 - In de `zh-cn` cultuur, LUIS wordt verwacht dat de vereenvoudigd Chinees tekenset in plaats van de traditionele tekenset.
 - De namen van de intenties, entiteiten, functies en reguliere expressies mogelijk Chinese of Romeins tekens.
 - Zie de [Naslag Gids met vooraf gedefinieerde domeinen](luis-reference-prebuilt-domains.md) voor informatie over welke vooraf ontwikkelde domeinen worden ondersteund in de `zh-cn`-cultuur.
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>\* Opmerkingen bij de Japanse ondersteuning

 - Omdat LUIS geen syntactische analyse biedt en het verschil tussen Keigo en informele Japans niet begrijpt, moet u gebruikmaken van de verschillende niveaus van formaliteit als voorbeelden van training voor uw toepassingen.
     - でございます is niet gelijk zijn aan です.
     - です is niet gelijk zijn aan だ.

### <a name="text-analytics-support-notes"></a>** Opmerkingen bij de ondersteuning voor text analytics
Tekstanalyse keyPhrase bevat vooraf gedefinieerde entiteits- en sentiment-analyse. Alleen Portugees wordt ondersteund voor overenting: `pt-PT` en `pt-BR`. Alle andere culturen worden ondersteund op het niveau van de primaire cultuur. Meer informatie over de Text Analytics [ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages).

### <a name="speech-api-supported-languages"></a>Talen voor spraak-API die wordt ondersteund
Zie spraak [ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) voor talen voor spraak dicteren modus.

### <a name="bing-spell-check-supported-languages"></a>Bing Spell Check-ondersteunde talen
Zie Bing spellingcontrole [ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) voor een lijst van ondersteunde talen en status.

## <a name="rare-or-foreign-words-in-an-application"></a>Zeldzame of refererende woorden in een toepassing
In de `en-us` cultuur, LUIS te onderscheiden van de meest Nederlandse woorden, met inbegrip van slang leren. In de `zh-cn` cultuur, LUIS leert te onderscheiden van de meeste Chinese tekens. Als u een zeldzame woord in `en-us` of teken in `zh-cn`, en u ziet dat LUIS lijkt te onderscheiden van dit woord of teken, kunt u dit woord toevoegen of teken naar een [functie woordgroep lijst](luis-how-to-add-features.md). Bijvoorbeeld, moeten woorden buiten de cultuur van de toepassing, dat wil zeggen, woorden in vreemde talen--worden toegevoegd aan een functie woordgroep-lijst. 

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Hybride-talen
Hybride talen combineren woorden uit twee culturen zoals Engels en Chinees. Deze talen worden niet ondersteund in LUIS omdat een app is gebaseerd op een enkele cultuur.

## <a name="tokenization"></a>Tokeniseren
Als u machine learning, LUIS een utterance opgesplitst in [tokens](luis-glossary.md#token) op basis van cultuur.

|Taal|  elke ruimte of speciaal teken | niveau tekens|samengestelde woorden|[tokens entiteit geretourneerd](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Chinees||✔||✔|
|Nederlands|||✔|✔|
|Engels (en-us)|✔ ||||
|Frans (fr-FR)|✔||||
|Frans (fr-CA)|✔||||
|Duits|||✔|✔|
| Hindi |✔|-|-|-|-|
|Italiaans|✔||||
|Japans||||✔|
|Koreaans||✔||✔|
|Portugees (Brazilië)|✔||||
|Spaans (es-ES)|✔||||
|Spaans (es-MX)|✔||||

### <a name="custom-tokenizer-versions"></a>Aangepaste tokenizer-versies

De volgende cult uren hebben aangepaste tokenizer-versies:

|Culture|Version|Doel|
|--|--|--|
|Duits<br>`de-de`|1.0.0|Tokenizes woorden door ze te splitsen met behulp van een tokenizer op basis van machine learning dat probeert samengestelde woorden op te splitsen in hun afzonderlijke onderdelen.<br>Als een gebruiker `Ich fahre einen krankenwagen` opgeeft als een utterance, wordt deze ingesteld op `Ich fahre einen kranken wagen`. Het markeren van `kranken` en `wagen` onafhankelijk als verschillende entiteiten toestaan.|
|Duits<br>`de-de`|1.0.2|Tokenizes woorden door ze te splitsen op spaties.<br> Als een gebruiker `Ich fahre einen krankenwagen` opgeeft als een utterance, blijft het één token. @No__t-0 is dus als één entiteit gemarkeerd. |

### <a name="migrating-between-tokenizer-versions"></a>Migreren tussen tokenizer-versies
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID. 

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

Tokeniseren gebeurt op het niveau van de app. Er wordt geen ondersteuning geboden voor tokenisatie op versie niveau. 

[Importeer het bestand als een nieuwe app](luis-how-to-start-new-app.md#import-an-app-from-file), in plaats van een versie. Deze actie betekent dat de nieuwe app een andere app-ID heeft, maar gebruikmaakt van de Tokenizer-versie die is opgegeven in het bestand. 
