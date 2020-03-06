---
title: Stream-codec gecomprimeerde audio met de Speech SDK-Speech-Service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het streamen van gecomprimeerde audio naar de speech-service met de spraak-SDK. Beschikbaar voor C++, C#en Java voor Linux, java in Android en objectief-C in Ios.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 2b530da06b02091ce66ff7c116f3e17ddcc22497
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331107"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Gecomprimeerde audio-invoer van codec gebruiken met de Speech SDK

De **gecomprimeerde audio-invoer stroom** -API van de Speech SDK biedt een manier om gecomprimeerde audio naar de spraak service te streamen met behulp van PullStream of PushStream.

> [!IMPORTANT]
> Streaming gecomprimeerde invoer audio wordt momenteel ondersteund voor C++, C#en Java op Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). Het wordt ook ondersteund voor [java in Android](how-to-use-codec-compressed-audio-input-streams-android.md) en [objectief-C in Ios-](how-to-use-codec-compressed-audio-input-streams-ios.md) platform.
> Speech SDK-versie 1.7.0 of hoger is vereist (versie 1.10.0 of hoger voor RHEL 8, CentOS 8).

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

Op RHEL/CentOS 8:

```sh
sudo yum install gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free
```

> [!NOTE]
> Volg in RHEL/CentOS 8 de instructies voor het [configureren van openssl voor Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

## <a name="example-code-using-codec-compressed-audio-input"></a>Voorbeeld code met behulp van gecomprimeerde audio-invoer van de codec

Als u een gecomprimeerde audio-indeling naar de speech-service wilt streamen, maakt u `PullAudioInputStream` of `PushAudioInputStream`. Maak vervolgens een `AudioConfig` van een instantie van uw Stream-klasse, waarbij u de compressie-indeling van de stroom opgeeft.

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
* [Zie spraak herkennen in Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
