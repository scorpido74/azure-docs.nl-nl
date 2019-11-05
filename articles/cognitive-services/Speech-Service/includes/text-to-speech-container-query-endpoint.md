---
title: Een query uitvoeren op het container eindpunt van tekst naar spraak
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491182"
---
De container bevat [op rest gebaseerde eind punt-api's](../rest-text-to-speech.md). Er zijn veel [voorbeeld broncode projecten](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) voor platform-, Framework-en taal variaties beschikbaar.

Met de *standaard-tekst-naar-spraak* -container moet u vertrouwen op de land instelling en de stem van de afbeeldings code die u hebt gedownload. Als u bijvoorbeeld het label `latest` hebt gedownload, is de standaard land instelling `en-US` en de `JessaRUS`e stem. Het argument `{VOICE_NAME}` wordt dan [`en-US-JessaRUS`](../language-support.md#standard-voices). Zie het onderstaande voor beeld SSML:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Voor *aangepaste tekst-naar-spraak* moet u echter de **spraak of het model** ophalen van de [aangepaste Voice Portal](https://aka.ms/custom-voice-portal). De naam van het aangepaste model is synoniem met de naam van de stem. Ga naar de pagina **training** en kopieer de **spraak/model** die u wilt gebruiken als `{VOICE_NAME}` argument.
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

Laten we een HTTP POST-aanvraag maken, waardoor er een paar koppen en een gegevens lading worden opgegeven. Vervang de tijdelijke aanduiding `{VOICE_NAME}` door uw eigen waarde.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Deze opdracht:

* Construct een HTTP POST-aanvraag voor het `speech/synthesize/cognitiveservices/v1`-eind punt.
* Hiermee geeft u een `Accept`-header van `audio/*`
* Hiermee geeft u een `Content-Type`-header van `application/ssml+xml`. Zie [aanvraag tekst](../rest-text-to-speech.md#request-body)voor meer informatie.
* Hiermee geeft u een `X-Microsoft-OutputFormat`-header van `riff-16khz-16bit-mono-pcm`op voor meer opties Zie [audio-uitvoer](../rest-text-to-speech.md#audio-outputs).
* Hiermee wordt de [SSML-aanvraag (Speech synthese Markup Language)](../speech-synthesis-markup.md) verzonden die de `{VOICE_NAME}` aan het eind punt heeft gegeven.