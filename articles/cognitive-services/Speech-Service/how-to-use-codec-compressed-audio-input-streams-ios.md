---
title: Streaming-codec gecomprimeerd met de Speech SDK op iOS
titleSuffix: Azure Cognitive Services
description: Meer informatie over het streamen van gecomprimeerde audio naar Azure speech Services met de Speech SDK op iOS.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: chlandsi
ms.openlocfilehash: 1d78c690fd07eb974418f0ea17d71d1f394d863d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109578"
---
# <a name="how-to-use-codec-compressed-audio-input-with-the-speech-sdk-on-ios"></a>Procedure: compressie van gecomprimeerde audio-invoer met de Speech SDK op iOS gebruiken

De **gecomprimeerde audio-invoer stroom** -API van de Speech SDK biedt een manier om gecomprimeerde audio naar de spraak service te streamen met behulp van een pull-of push stroom.

> [!IMPORTANT]
> Speech SDK versie 1.7.0 of hoger is vereist voor het streamen van gecomprimeerde audio op iOS. Het wordt ook ondersteund voor [ C++, C#en Java op Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9)](how-to-use-codec-compressed-audio-input-streams.md) en [java in Android.](how-to-use-codec-compressed-audio-input-streams-android.md)

Zie de mainline speech-documentatie voor WAV/PCM. Buiten WAV/PCM worden de volgende in de codec gecomprimeerde invoer indelingen ondersteund:

- MP3
- OPUS/OGG
- FLAC
- ALAW in WAV-container
- MULAW in WAV-container

## <a name="prerequisites"></a>Vereisten

Het verwerken van gecomprimeerde audio wordt geïmplementeerd met behulp van [gstreamer](https://gstreamer.freedesktop.org). Deze functies kunnen om licentie redenen niet worden geleverd bij de SDK, maar een wrapper-bibliotheek met deze functies moet worden gebouwd door toepassings ontwikkelaars en worden geleverd met de apps met behulp van de SDK.

Als u deze wrapper-bibliotheek wilt maken, moet u eerst de [gstreamer-SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)downloaden en installeren. Down load vervolgens het Xcode-project voor de [wrapper-bibliotheek](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Open het project in Xcode en bouw het voor het **algemene IOS-apparaat** doel--het zal niet werken voor een specifiek doel.

Met de stap build wordt een dynamisch Framework gegenereerd met een dynamische bibliotheek voor alle benodigde architecturen met de naam van `GStreamerWrapper.framework`.

Dit framework moet worden opgenomen in alle apps die gebruikmaken van gecomprimeerde audio stromen met de speech Services SDK.

Pas de volgende instellingen in uw Xcode-project toe om dit te bewerkstelligen:

1. Kopieer de `GStreamerWrapper.framework` die u zojuist hebt gemaakt en het Framework van de Cognitive Services Speech SDK, die u [hier](https://aka.ms/csspeech/iosbinary)kunt downloaden, naar de map met uw voorbeeld project.
1. Wijzig de paden in de kaders in de _project instellingen_.
   1. Voeg op het tabblad **Algemeen** onder de kop **Inge sloten binaire bestanden** de SDK-bibliotheek toe als Framework: **Voeg inge sloten binaire bestanden** toe > **Voeg andere toe...** > Navigeer naar de map die u hebt gekozen en selecteer beide frameworks.
   1. Ga naar het tabblad **Build Settings** en activeer **alle** instellingen.
1. Voeg de map `$(SRCROOT)/..` toe aan de _Framework Search Paths_ onder de kop **Search Paths**.

## <a name="example-code-using-codec-compressed-audio-input"></a>Voorbeeld code met behulp van gecomprimeerde audio-invoer van de codec

Als u een gecomprimeerde audio-indeling naar de spraak Services wilt streamen, maakt u een `SPXPullAudioInputStream` of `SPXPushAudioInputStream`.

Het volgende code fragment laat zien hoe u een `SPXAudioConfiguration` maakt op basis van een exemplaar van een `SPXPushAudioInputStream`en hoe MP3 wordt opgegeven als de compressie-indeling van de stroom.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=66-77&highlight=2-11)]

In het volgende fragment ziet u hoe gecomprimeerde audio gegevens kunnen worden gelezen uit een bestand en worden gepompt in de `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=105-151&highlight=19-44)]

## <a name="next-steps"></a>Volgende stappen

- [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
- [Zie spraak herkennen in Java](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
