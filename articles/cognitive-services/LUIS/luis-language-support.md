---
title: Taalondersteuning - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS heeft een verscheidenheid aan functies binnen de service. Niet alle functies zijn op dezelfde taal pariteit. Zorg ervoor dat de functies waarin u geïnteresseerd bent, worden ondersteund in de taalcultuur die u target. Een LUIS-app is cultuurspecifiek en kan niet worden gewijzigd zodra deze is ingesteld.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 4b6d954d06f09bef5240bddc4860ddbc83513d69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220851"
---
# <a name="language-and-region-support-for-luis"></a>Ondersteuning voor taal en regio voor LUIS

LUIS heeft een verscheidenheid aan functies binnen de service. Niet alle functies zijn op dezelfde taal pariteit. Zorg ervoor dat de functies waarin u geïnteresseerd bent, worden ondersteund in de taalcultuur die u target. Een LUIS-app is cultuurspecifiek en kan niet worden gewijzigd zodra deze is ingesteld.

## <a name="multi-language-luis-apps"></a>Meertalige LUIS-apps

Als je een meertalige LUIS-clientapplicatie nodig hebt, zoals een chatbot, heb je een aantal opties. Als LUIS alle talen ondersteunt, ontwikkelt u voor elke taal een LUIS-app. Elke LUIS-app heeft een unieke app-id en eindpuntlogboek. Als u taalbegrip moet bieden voor een taal die LUIS niet ondersteunt, u [de Api van Microsoft Translator](../Translator/translator-info-overview.md) gebruiken om de utterance in een ondersteunde taal te vertalen, de utterance in te dienen bij het LUIS-eindpunt en de resulterende scores te ontvangen.

## <a name="languages-supported"></a>Ondersteunde talen

LUIS begrijpt uitingen in de volgende talen:

| Taal |Landinstelling  |  Vooraf gebouwd domein | Vooraf gebouwde entiteit | Aanbevelingen voor woordgroepen | **[Tekstanalyse](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Sentiment en<br>Trefwoorden)|
|--|--|:--:|:--:|:--:|:--:|
| Amerikaans Engels |`en-US` | ✔ | ✔  |✔|✔|
| Arabisch (voorbeeld - modern standaard Arabisch) |`ar-AR`|-|-|-|-|
| *[Chinees](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Nederlands |`nl-NL` |✔|  -   |-|✔|
| Frans (Frankrijk) |`fr-FR` |✔| ✔ |✔ |✔|
| Frans (Canada) |`fr-CA` |-|   -   |-|✔|
| Duits |`de-DE` |✔| ✔ |✔ |✔|
| Hindi | `hi-IN`|-|-|-|-|
| Italiaans |`it-IT` |✔| ✔ |✔|✔|
| *[Japans](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Alleen sleutelzin|
| Koreaans |`ko-KR` |✔|   -   |-|Alleen sleutelzin|
| Portugees (Brazilië) |`pt-BR` |✔| ✔ |✔ |niet alle subculturen|
| Spaans (Spanje) |`es-ES` |✔| ✔ |✔|✔|
| Spaans (Mexico)|`es-MX` |-|  -   |✔|✔|
| Turks | `tr-TR` |✔|-|-|Sentiment alleen|

Taalondersteuning varieert voor [vooraf gebouwde entiteiten](luis-reference-prebuilt-entities.md) en [vooraf gebouwde domeinen.](luis-reference-prebuilt-domains.md)

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>*Japanse ondersteuningsnotities

 - Omdat LUIS geen syntactische analyse biedt en het verschil tussen Keigo en informeel Japans niet begrijpt, moet u de verschillende niveaus van formaliteit opnemen als trainingsvoorbeelden voor uw toepassingen.
     - Het is niet hetzelfde als het zelfde.
     - Het is niet hetzelfde als.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Door spraak-API ondersteunde talen
Zie [Taalondersteunde talen](../speech-service/speech-to-text.md) voor spraakdicteermodus.

### <a name="bing-spell-check-supported-languages"></a>Ondersteunde talen bing Spell Check
Zie Ondersteunde [talen](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) van Bing Spell Check voor een lijst met ondersteunde talen en status.

## <a name="rare-or-foreign-words-in-an-application"></a>Zeldzame of vreemde woorden in een toepassing
In `en-us` de cultuur leert LUIS de meeste Engelse woorden te onderscheiden, waaronder slang. In `zh-cn` de cultuur leert LUIS de meeste Chinese karakters te onderscheiden. Als u een zeldzaam `en-us` woord `zh-cn`of teken in, en je ziet dat LUIS lijkt niet in staat om dat woord of teken te onderscheiden, u dat woord of teken toe te voegen aan een [woordlijst functie](luis-how-to-add-features.md). Woorden buiten de cultuur van de toepassing - dat wil zeggen vreemde woorden - moeten bijvoorbeeld worden toegevoegd aan een woordlijstfunctie.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Hybride talen
Hybride talen combineren woorden uit twee culturen zoals Engels en Chinees. Deze talen worden niet ondersteund in LUIS omdat een app is gebaseerd op één cultuur.

## <a name="tokenization"></a>Tokenisatie
Om machine learning uit te voeren, breekt LUIS een utterance in [tokens](luis-glossary.md#token) op basis van cultuur.

|Taal|  elke ruimte of speciaal teken | tekenniveau|samengestelde woorden|[tokenized entiteit geretourneerd](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Arabisch|||||
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

### <a name="custom-tokenizer-versions"></a>Aangepaste tokenizerversies

De volgende culturen hebben aangepaste tokenizer-versies:

|Cultuur|Versie|Doel|
|--|--|--|
|Duits<br>`de-de`|1.0.0|Tokenizes woorden door ze te splitsen met behulp van een op machine learning gebaseerde tokenizer die samengestelde woorden probeert op te splitsen in hun afzonderlijke componenten.<br>Als een `Ich fahre einen krankenwagen` gebruiker als utterance wordt `Ich fahre einen kranken wagen`ingeschakeld, wordt deze ingeschakeld om . Het toestaan van `kranken` `wagen` het markeren van en onafhankelijk als verschillende entiteiten.|
|Duits<br>`de-de`|1.0.2|Tokenizes woorden door ze op te splitsen op spaties.<br> als een `Ich fahre einen krankenwagen` gebruiker als een utterance invoert, blijft het één token. Wordt `krankenwagen` dus gemarkeerd als één entiteit. |

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

Tokenisatie gebeurt op app-niveau. Er is geen ondersteuning voor tokenisatie op versieniveau.

[Importeer het bestand als een nieuwe app,](luis-how-to-start-new-app.md)in plaats van een versie. Deze actie betekent dat de nieuwe app een andere app-id heeft, maar de tokenizer-versie gebruikt die in het bestand is opgegeven.
