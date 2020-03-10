---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943726"
---
Als u een gecomprimeerde audio-indeling naar de speech-service wilt streamen, maakt u een `SPXPullAudioInputStream` of `SPXPushAudioInputStream`.

Het volgende code fragment laat zien hoe u een `SPXAudioConfiguration` maakt op basis van een exemplaar van een `SPXPushAudioInputStream`, waarbij een MP3 wordt opgegeven als de compressie-indeling van de stroom.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

In het volgende fragment ziet u hoe gecomprimeerde audio gegevens kunnen worden gelezen uit een bestand en worden gepompt in de `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
