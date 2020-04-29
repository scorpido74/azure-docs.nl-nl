---
title: Een query uitvoeren op het container eindpunt van tekst naar spraak
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b2a621a23a81e4fb4f47e7c99d780211973e30a0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81275520"
---
De container bevat [op rest gebaseerde eind punt-api's](../rest-text-to-speech.md). Er zijn veel [voorbeeld broncode projecten](https://github.com/Azure-Samples/Cognitive-Speech-TTS) voor platform-, Framework-en taal variaties beschikbaar.

Met de *standaard-tekst-naar-spraak* -container moet u vertrouwen op de land instelling en de stem van de afbeeldings code die u hebt gedownload. Als u bijvoorbeeld de `latest` tag hebt gedownload, is `en-US` de standaard land instelling en `JessaRUS` de stem. Het `{VOICE_NAME}` argument zou dan zijn [`en-US-JessaRUS`](../language-support.md#standard-voices). Zie het onderstaande voor beeld SSML:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Voor *aangepaste tekst-naar-spraak* moet u echter de **spraak of het model** ophalen van de [aangepaste Voice Portal](https://aka.ms/custom-voice-portal). De naam van het aangepaste model is synoniem met de naam van de stem. Ga naar de pagina **training** en kopieer de **spraak/model** die u als `{VOICE_NAME}` argument wilt gebruiken.
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
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Met deze opdracht gebeurt het volgende:

* Construct een HTTP POST-aanvraag voor het `speech/synthesize/cognitiveservices/v1` eind punt.
* Hiermee geeft `Accept` u een kop van`audio/*`
* Hiermee geeft `Content-Type` u een `application/ssml+xml`kop van op. Zie [aanvraag tekst](../rest-text-to-speech.md#request-body)voor meer informatie.
* Hiermee geeft `X-Microsoft-OutputFormat` u een `riff-16khz-16bit-mono-pcm`kop van op. Zie [audio-uitvoer](../rest-text-to-speech.md#audio-outputs)voor meer opties.
* Verzendt de [SSML-aanvraag (Speech synthese Markup Language)](../speech-synthesis-markup.md) `{VOICE_NAME}` die is gegeven aan het eind punt.