---
title: Eindpunt van tekst-naar-spraakcontainer
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b2a621a23a81e4fb4f47e7c99d780211973e30a0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81275520"
---
De container biedt [REST-gebaseerde eindpuntAPI's](../rest-text-to-speech.md). Er zijn veel [voorbeeldbroncodeprojecten](https://github.com/Azure-Samples/Cognitive-Speech-TTS) beschikbaar voor platform-, framework- en taalvariaties.

Met de *standaardtekst-naar-spraak-container* moet u vertrouwen op de land- en stem van de afbeeldingstag die u hebt gedownload. Als u bijvoorbeeld de `latest` tag hebt gedownload, `en-US` is `JessaRUS` de standaardlandinstelling en de stem. Het `{VOICE_NAME}` argument zou [`en-US-JessaRUS`](../language-support.md#standard-voices)dan zijn . Zie hieronder het voorbeeld SSML:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Voor *aangepaste tekst-naar-spraak* moet u echter de **spraak /het model** verkrijgen van de aangepaste [spraakportal.](https://aka.ms/custom-voice-portal) De aangepaste modelnaam is synoniem met de naam van de stem. Navigeer naar de **pagina Training** en kopieer het `{VOICE_NAME}` **spraak/model** om als argument te gebruiken.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Aangepast spraakmodel - spraaknaam":::

Zie hieronder het voorbeeld SSML:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Laten we een HTTP POST-aanvraag samenstellen, met een paar headers en een gegevenspayload. Vervang `{VOICE_NAME}` de tijdelijke aanduiding door uw eigen waarde.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Met deze opdracht gebeurt het volgende:

* Construeert een HTTP POST-aanvraag voor het `speech/synthesize/cognitiveservices/v1` eindpunt.
* Hiermee `Accept` geeft u een koptekst op van`audio/*`
* Hiermee `Content-Type` geeft `application/ssml+xml`u een koptekst op van , voor meer informatie, zie [aanvraaghoofd](../rest-text-to-speech.md#request-body).
* Hiermee `X-Microsoft-OutputFormat` geeft `riff-16khz-16bit-mono-pcm`u een koptekst op van , voor meer opties zie [audio-uitvoer](../rest-text-to-speech.md#audio-outputs).
* Hiermee wordt de [SSML-aanvraag (Speech Synthesis Markup Language)](../speech-synthesis-markup.md) naar `{VOICE_NAME}` het eindpunt geleid.