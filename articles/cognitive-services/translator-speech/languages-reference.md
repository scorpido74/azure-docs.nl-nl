---
title: Methode voor Translator Speech-API talen
titleSuffix: Azure Cognitive Services
description: Gebruik de Translator Speech-API talen methode.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 46ac3928238382f56db5a799226bd3d9243b7ca2
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966414"
---
# <a name="translator-speech-api-languages"></a>Translator Speech-API: Languages

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Translator Speech breidt de lijst met talen die door de services worden ondersteund, voortdurend uit. Gebruik deze API om de set talen te detecteren die momenteel beschikbaar zijn voor gebruik met de Translator Speech-Service.

Code voorbeelden van het gebruik van de API voor het verkrijgen van beschik bare talen zijn beschikbaar via de [micro soft Translator github-site](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Opmerkingen bij de implementatie

### <a name="get-languages"></a>/Languages ophalen

Er is een grote set talen beschikbaar voor het transcriberen van spraak, het vertalen van de vertaalde tekst en het produceren van een gesynthesizerde spraak van de vertaling.

Een client maakt gebruik `scope` van de query parameter om te bepalen in welke talen de para meter is geïnteresseerd.

* **Spraak naar tekst:** Gebruik query parameter `scope=speech` om de set beschik bare talen op te halen om spraak naar tekst te transcriberen.
* **Vertalen van tekst:** Gebruik query parameter `scope=text` om de set beschik bare talen op te halen voor het vertalen van transcribed tekst.
* **Tekst-naar-spraak:**  Gebruik query parameter `scope=tts` om de set talen en stemmen op te halen die beschikbaar zijn om vertaalde tekst weer in spraak te voorzien.

Een client kan meerdere sets tegelijk ophalen door een door komma's gescheiden lijst met opties op te geven. Bijvoorbeeld `scope=speech,text,tts`.

Een geslaagde reactie is een JSON-object met een eigenschap voor elke aangevraagde set.

```
{
    "speech": {
        //... Set of languages supported for speech-to-text
    },
    "text": {
        //... Set of languages supported for text translation
    },
    "tts": {
        //... Set of languages supported for text-to-speech
    }
}
```

Omdat een client de `scope` query parameter kan gebruiken om te selecteren welke sets ondersteunde talen moeten worden geretourneerd, mag een echt antwoord alleen een subset van alle hierboven weer gegeven eigenschappen bevatten.

De waarde die bij elke eigenschap wordt gegeven, is als volgt.

### <a name="speech-to-text-speech"></a>Spraak naar tekst (spraak)

De waarde die is gekoppeld aan de eigenschap voor spraak naar tekst `speech`, is een woorden lijst van paren (sleutel, waarde). Elke sleutel identificeert een taal die wordt ondersteund voor spraak naar tekst. De sleutel is de id die door de client wordt door gegeven aan de API. De waarde die is gekoppeld aan de sleutel is een object met de volgende eigenschappen:

* `name`: Weergave naam van de taal.
* `language`: Taal code van de gekoppelde geschreven taal. Zie "tekst transactie" hieronder.
Een voor beeld is:

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Tekst omzetting (tekst)

De waarde die is gekoppeld `text` aan de eigenschap is ook een woorden lijst waarbij elke sleutel een taal identificeert die wordt ondersteund voor tekst vertaling. De waarde die is gekoppeld aan de sleutel, beschrijft de taal:

* `name`: Weergave naam van de taal.
* `dir`: Directionality `rtl` voor talen die van rechts naar links worden geschreven of `ltr` van links naar rechts.

Een voor beeld is:

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Tekst-naar-spraak (TTS)

De waarde die is gekoppeld aan de eigenschap tekst-naar-spraak, TTS, is ook een woorden lijst waarbij elke sleutel een ondersteunde spraak identificeert. De kenmerken van een spraak object zijn:

* `displayName`: Weergave naam voor de stem.
* `gender`: Geslacht van de stem (mannelijk of vrouwelijk).
* `locale`: Taal code van de stem met primaire taal subtag en regio subtag.
* `language`: Taal code van de gekoppelde geschreven taal.
* `languageName`: Weergave naam van de taal.
* `regionName`: Weergave naam van de regio voor deze taal.

Een voor beeld is:

```
{
    "tts": {
        "en-US-Zira": {
            "displayName": "Zira",
            "gender": "female",
            "locale": "en-US",
            "languageName": "English",
            "regionName": "United States",
            "language": "en"
        }
}
```

### <a name="localization"></a>Lokalisatie
De service retourneert alle namen in de taal van de ' Accept-Language '-header, voor alle talen die worden ondersteund in tekst vertalingen.

### <a name="response-class-status-200"></a>Response-klasse (status 200)
Het object dat de set ondersteunde talen beschrijft.

ModelExample-waarde:

Langagues {Speech (object, optioneel), Text (object, optioneel), TTS (object, optioneel)}

### <a name="headers"></a>Headers

|Header|Description|type|
:--|:--|:--|
X-RequestId|De waarde die door de server wordt gegenereerd om de aanvraag te identificeren en wordt gebruikt voor het oplossen van problemen.|string|

### <a name="parameters"></a>Parameters

|Parameter|Description|Parametertype|Gegevenstype|
|:--|:--|:--|:--|
|api-version    |De versie van de API die door de client is aangevraagd. Toegestane waarden zijn: `1.0`.|query|string|
|bereik  |Sets van ondersteunde talen of stemmen die moeten worden geretourneerd naar de client. Deze para meter wordt opgegeven als een door komma's gescheiden lijst met tref woorden. De volgende tref woorden zijn beschikbaar:<ul><li>`speech`: Biedt de set talen die worden ondersteund voor het transcriberen van spraak.</li><li>`tts`: Biedt de set stemmen die worden ondersteund voor conversie van tekst spraak.</li><li>`text`: Biedt de set talen die worden ondersteund voor het vertalen van tekst.</li></ul>Als er geen waarde is opgegeven, is de waarde `scope` van standaard `text`ingesteld op.|query|string|
|X-ClientTraceId    |Een door de client gegenereerde GUID die wordt gebruikt om een aanvraag te traceren. Om het oplossen van problemen te vereenvoudigen, moeten clients een nieuwe waarde opgeven bij elke aanvraag en deze registreren.|koptekst|string|
|Accept-Language    |Sommige velden in het antwoord zijn namen van talen of regio's. Gebruik deze para meter om de taal te definiëren waarin de namen worden geretourneerd. De taal wordt opgegeven door een juist opgemaakte BCP 47-taal code op te geven. Selecteer een tag in de lijst met taal-id's die met het `text` bereik zijn geretourneerd. Voor niet-ondersteunde talen zijn de namen beschikbaar in de Engelse taal.<br/>Gebruik bijvoorbeeld de waarde `fr` voor het aanvragen van namen in het Frans of gebruik de waarde `zh-Hant` om namen aan te vragen in traditioneel Chinees.|koptekst|string|

### <a name="response-messages"></a>Antwoord berichten

|HTTP-status code|Reason|
|:--|:--|
|400|Ongeldige aanvraag. Controleer de invoer parameters om er zeker van te zijn dat ze geldig zijn. Het object Response bevat een gedetailleerde beschrijving van de fout.|
|429|Te veel aanvragen.|
|500|Er is een fout opgetreden. Als de fout zich blijft voordoen, meldt u deze met de client Trace Identifier (X-ClientTraceId) of de aanvraag-id (X-id-aanvraag).|
|503|De server is tijdelijk niet beschikbaar. Voer de aanvraag opnieuw uit. Als de fout zich blijft voordoen, meldt u deze met de client Trace Identifier (X-ClientTraceId) of de aanvraag-id (X-id-aanvraag).|
