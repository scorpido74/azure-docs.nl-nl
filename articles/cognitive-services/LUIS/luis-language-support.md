---
title: Taal ondersteuning-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS heeft diverse functies binnen de service. Niet alle functies bevinden zich in dezelfde taal pariteit. Zorg ervoor dat de functies die u interesseen, worden ondersteund in de taal cultuur die u wilt richten. Een LUIS-app is specifiek voor een cultuur en kan niet meer worden gewijzigd nadat deze is ingesteld.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: 83fd06078500be7b5bd58e9ea92d957f9d77f892
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904209"
---
# <a name="language-and-region-support-for-luis"></a>Taal-en regio ondersteuning voor LUIS

LUIS heeft diverse functies binnen de service. Niet alle functies bevinden zich in dezelfde taal pariteit. Zorg ervoor dat de functies die u interesseen, worden ondersteund in de taal cultuur die u wilt richten. Een LUIS-app is specifiek voor een cultuur en kan niet meer worden gewijzigd nadat deze is ingesteld.

## <a name="multi-language-luis-apps"></a>LUIS-apps met meerdere talen

Als u een LUIS-client toepassing met meerdere talen nodig hebt, zoals een chatbot, hebt u een aantal opties. Als LUIS alle talen ondersteunt, ontwikkelt u een LUIS-app voor elke taal. Elke LUIS-app heeft een unieke App-ID en een eindpunt logboek. Als u taal inzicht moet bieden voor een taal LUIS ondersteunt, kunt u de [micro soft Translator-API](../Translator/translator-info-overview.md) gebruiken om de utterance te vertalen in een ondersteunde taal, de utterance naar het Luis-eind punt te verzenden en de resulterende scores te ontvangen.

## <a name="languages-supported"></a>Ondersteunde talen

LUIS begrijpt uitingen in de volgende talen:

| Taal |Landinstelling  |  Vooraf gebouwd domein | Vooraf gebouwde entiteit | Aanbevelingen voor woordgroepen lijst | **[tekst analyse](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Sentiment en<br>Woord|
|--|--|:--:|:--:|:--:|:--:|
| Amerikaans-Engels |`en-US` | ✔ | ✔  |✔|✔|
| *[Chinees](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Nederlands |`nl-NL` |✔|  -   |-|✔|
| Frans (Frank rijk) |`fr-FR` |✔| ✔ |✔ |✔|
| Frans (Canada) |`fr-CA` |-|   -   |-|✔|
| Duits |`de-DE` |✔| ✔ |✔ |✔|
| Hindi | `hi-IN`|-|-|-|-|
| Italiaans |`it-IT` |✔| ✔ |✔|✔|
| *[Japans](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Alleen sleutel woorden|
| Koreaans |`ko-KR` |✔|   -   |-|Alleen sleutel woorden|
| Portugees (Brazilië) |`pt-BR` |✔| ✔ |✔ |niet alle subcultuuren|
| Spaans (Spanje) |`es-ES` |✔| ✔ |✔|✔|
| Spaans (Mexico)|`es-MX` |-|  -   |✔|✔|
| Turks | `tr-TR` |✔|-|-|Alleen sentiment|

Taal ondersteuning is afhankelijk van [vooraf gemaakte entiteiten](luis-reference-prebuilt-entities.md) en [vooraf gemaakte domeinen](luis-reference-prebuilt-domains.md).

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>\* Japanse ondersteunings opmerkingen

 - Omdat LUIS geen syntaxis analyse biedt en geen inzicht heeft in het verschil tussen Keigo en informele Japans, moet u de verschillende formaliteiten op het niveau van de voor beelden voor uw toepassingen opnemen.
     - でございます is niet hetzelfde als です.
     - です is niet hetzelfde als だ.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Ondersteunde talen voor spraak-API
Zie spraak [ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) voor talen voor spraak herkenning.

### <a name="bing-spell-check-supported-languages"></a>Bing Spellingcontrole ondersteunde talen
Zie Bing Spellingcontrole [ondersteunde talen](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) voor een lijst met ondersteunde talen en status.

## <a name="rare-or-foreign-words-in-an-application"></a>Zeldzame of afwijkende woorden in een toepassing
In de `en-us` cultuur leert LUIS om de meeste Engelse woorden te onderscheiden, met inbegrip van slang. In de `zh-cn` cultuur leert LUIS om de meeste Chinese tekens te onderscheiden. Als u een zeldzaam woord gebruikt in `en-us` of teken in `zh-cn`en u ziet dat LUIS niet kan worden onderscheiden van dat woord of teken, kunt u dat woord of teken toevoegen aan een [lijst functie van een woord groep](luis-how-to-add-features.md). Bijvoorbeeld: woorden buiten de cultuur van de toepassing, dat wil zeggen afwijkende woorden--moeten worden toegevoegd aan een woordgroepen lijst functie. 

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Hybride talen
Hybride talen combi neren woorden uit twee cult uren, zoals Engels en Chinees. Deze talen worden niet ondersteund in LUIS omdat een app is gebaseerd op één cultuur.

## <a name="tokenization"></a>Token
Om machine learning uit te voeren, breekt LUIS een utterance in [tokens](luis-glossary.md#token) op basis van de cultuur.

|Taal|  elke spatie of speciaal teken | teken niveau|samengestelde woorden|[tokend geretourneerde entiteit](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Chinees||✔||✔|
|Nederlands|||✔|✔|
|Engels (en-us)|✔ ||||
|Frans (fr-FR)|✔||||
|Frans (FR-CA)|✔||||
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

|Culture|Versie|Doel|
|--|--|--|
|Duits<br>`de-de`|1.0.0|Tokenizes woorden door ze te splitsen met behulp van een tokenizer op basis van machine learning dat probeert samengestelde woorden op te splitsen in hun afzonderlijke onderdelen.<br>Als een gebruiker `Ich fahre einen krankenwagen` als een utterance invoert, wordt deze `Ich fahre einen kranken wagen`. Het markeren van `kranken` en het `wagen` onafhankelijk als verschillende entiteiten toestaan.|
|Duits<br>`de-de`|1.0.2|Tokenizes woorden door ze te splitsen op spaties.<br> Als een gebruiker `Ich fahre einen krankenwagen` opgeeft als een utterance, blijft het één token. `krankenwagen` is dus als één entiteit gemarkeerd. |

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

[Importeer het bestand als een nieuwe app](luis-how-to-start-new-app.md), in plaats van een versie. Deze actie betekent dat de nieuwe app een andere app-ID heeft, maar gebruikmaakt van de Tokenizer-versie die is opgegeven in het bestand. 
