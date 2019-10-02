---
title: Stream-codec gecomprimeerde audio met de Speech SDK-Speech-Service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het streamen van gecomprimeerde audio naar Azure speech Services met de spraak-SDK. Beschikbaar voor C++, C#en Java voor Linux, java in Android en objectief-C in Ios.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 8f2896a6289ecaf4046d705da106636258cdadc5
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802242"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Gecomprimeerde audio-invoer van codec gebruiken met de Speech SDK

De **gecomprimeerde audio-invoer stroom** -API van de Speech SDK biedt een manier om gecomprimeerde audio naar de spraak service te streamen met behulp van PullStream of PushStream.

> [!IMPORTANT]
> Streaming gecomprimeerde invoer audio wordt momenteel ondersteund voor C++, C#en Java op Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9). Het wordt ook ondersteund voor [java in Android](how-to-use-codec-compressed-audio-input-streams-android.md) en [objectief-C in Ios-](how-to-use-codec-compressed-audio-input-streams-ios.md) platform.
> Speech SDK-versie 1.7.0 of hoger is vereist.

Zie de mainline speech-documentatie voor WAV/PCM.  Buiten WAV/PCM worden de volgende in de codec gecomprimeerde invoer indelingen ondersteund:

- MP3
- OPUS/OGG
- FLAC
- ALAW in WAV-container
- MULAW in WAV-container

## <a name="prerequisites"></a>Vereisten

Het verwerken van gecomprimeerde audio wordt geïmplementeerd met behulp van [gstreamer](https://gstreamer.freedesktop.org). Voor de reden van de licentie verlening gstreamer binaire bestanden niet worden gecompileerd en gekoppeld aan de spraak-SDK. Ontwikkel aars van toepassingen moeten het volgende op 18,04, 16,04 en Debian 9 installeren om gecomprimeerde invoer audio te gebruiken.

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>Voorbeeld code met behulp van gecomprimeerde audio-invoer van de codec

Als u een gecomprimeerde audio-indeling naar de spraak Services wilt streamen, maakt u `PullAudioInputStream` of `PushAudioInputStream`. Maak vervolgens een `AudioConfig` van een exemplaar van uw Stream-klasse, waarbij u de compressie-indeling van de stroom opgeeft.

We gaan ervan uit dat u een invoer stroom klasse hebt met de naam `myPushStream` en gebruikmaakt van OPUS/OGG. Uw code kan er als volgt uitzien:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
var audioFormat = AudioStreamFormat.GetCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
var audioConfig = AudioConfig.FromStreamInput(myPushStream, audioFormat);

var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

var result = await recognizer.RecognizeOnceAsync();

var text = result.GetText();
```

## <a name="next-steps"></a>Volgende stappen

- [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
- [Zie voor het herkennen van gesproken tekst in C#](quickstart-csharp-dotnet-windows.md)
