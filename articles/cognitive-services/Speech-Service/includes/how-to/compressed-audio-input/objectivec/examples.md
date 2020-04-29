---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422099"
---
Als u een gecomprimeerde audio-indeling naar de speech-service wilt streamen, maakt u een `SPXPullAudioInputStream` of `SPXPushAudioInputStream`.

Het volgende code fragment laat zien hoe u `SPXAudioConfiguration` een van een exemplaar van `SPXPushAudioInputStream`een maakt, en een MP3 opgeeft als de compressie-indeling van de stroom.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

In het volgende fragment ziet u hoe gecomprimeerde audio gegevens kunnen worden gelezen uit een bestand en worden gepompt in de `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
