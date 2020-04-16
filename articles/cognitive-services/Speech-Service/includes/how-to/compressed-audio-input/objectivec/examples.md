---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422099"
---
Als u wilt streamen in een gecomprimeerde `SPXPullAudioInputStream` audio-indeling naar de spraakservice, maakt u een of . `SPXPushAudioInputStream`

In het volgende fragment `SPXAudioConfiguration` ziet u hoe `SPXPushAudioInputStream`u een exemplaar van een , het opgeven van een MP3 als de compressie-indeling van de stream.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

Het volgende fragment laat zien hoe gecomprimeerde audiogegevens kunnen `SPXPushAudioInputStream`worden gelezen uit een bestand en in de .

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
