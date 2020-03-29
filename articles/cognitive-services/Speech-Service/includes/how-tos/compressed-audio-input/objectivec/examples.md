---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943726"
---
Als u wilt streamen in een gecomprimeerde `SPXPullAudioInputStream` audio-indeling naar de spraakservice, maakt u een of . `SPXPushAudioInputStream`

In het volgende fragment `SPXAudioConfiguration` ziet u hoe `SPXPushAudioInputStream`u een exemplaar van een , het opgeven van een MP3 als de compressie-indeling van de stream.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

Het volgende fragment laat zien hoe gecomprimeerde audiogegevens kunnen `SPXPushAudioInputStream`worden gelezen uit een bestand en in de .

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
