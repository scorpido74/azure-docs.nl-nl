---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: b565bf3bae61cf92133b957bb75fdae9545c030e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421861"
---
Als u wilt streamen in een gecomprimeerde `PullAudioInputStream` `PushAudioInputStream`audio-indeling naar de spraakservice, maakt of maakt u. Maak vervolgens `AudioConfig` een exemplaar van uw streamklasse en geeft de compressieindeling van de stream op.

Laten we aannemen dat u een `pushStream` invoerstreamklasse hebt genaamd en OPUS/OGG gebruikt. Uw code kan er als volgt uitzien:

```cpp
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

// ... omitted for brevity

 auto config =
    SpeechConfig::FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion"
    );

auto audioFormat =
    AudioStreamFormat::GetCompressedFormat(
        AudioStreamContainerFormat::OGG_OPUS
    );
auto audioConfig =
    AudioConfig::FromStreamInput(
        pushStream,
        audioFormat
    );

auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
auto result = recognizer->RecognizeOnceAsync().get();

auto text = result->Text;
```