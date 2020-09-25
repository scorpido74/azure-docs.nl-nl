---
title: Een query uitvoeren op het container eindpunt van tekst naar spraak
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 7e5ea8dcddce31a414d983d14fba483eb388d5d5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334669"
---
De container bevat [op rest gebaseerde eind punt-api's](../rest-text-to-speech.md). Er zijn veel [voorbeeld broncode projecten](https://github.com/Azure-Samples/Cognitive-Speech-TTS) voor platform-, Framework-en taal variaties beschikbaar.

Met de Standard-of Neural-tekst-naar-spraak-containers moet u vertrouwen op de land instelling en de stem van de afbeeldings code die u hebt gedownload. Als u bijvoorbeeld de tag hebt gedownload, `latest` is de standaard land instelling `en-US` en de `AriaRUS` stem. Het `{VOICE_NAME}` argument zou dan zijn [`en-US-AriaRUS`](../language-support.md#standard-voices) . Zie het onderstaande voor beeld SSML:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Voor *aangepaste tekst-naar-spraak* moet u echter de **spraak of het model** ophalen van de [aangepaste Voice Portal](https://aka.ms/custom-voice-portal). De naam van het aangepaste model is synoniem met de naam van de stem. Ga naar de pagina **training** en kopieer de **spraak/model** die u als argument wilt gebruiken `{VOICE_NAME}` .
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Aangepast spraak model-spraak naam":::

Zie het onderstaande voor beeld SSML:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Laten we een HTTP POST-aanvraag maken, waardoor er een paar koppen en een gegevens lading worden opgegeven. Vervang de `{VOICE_NAME}` tijdelijke aanduiding door uw eigen waarde.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>' > output.wav
```


Met deze opdracht gebeurt het volgende:

* Construct een HTTP POST-aanvraag voor het `speech/synthesize/cognitiveservices/v1` eind punt.
* Hiermee geeft u een `Accept` kop van `audio/*`
* Hiermee geeft u een `Content-Type` kop van `application/ssml+xml` op. Zie [aanvraag tekst](../rest-text-to-speech.md#request-body)voor meer informatie.
* Hiermee geeft u een `X-Microsoft-OutputFormat` kop van `riff-16khz-16bit-mono-pcm` op. Zie [audio-uitvoer](../rest-text-to-speech.md#audio-outputs)voor meer opties.
* Verzendt de [SSML-aanvraag (Speech synthese Markup Language)](../speech-synthesis-markup.md) die is gegeven `{VOICE_NAME}` aan het eind punt.