---
title: Stream-codec gecomprimeerde audio met de Speech SDK voor iOS-Speech-Service
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
ms.openlocfilehash: 9a66e4ecf2230caad233a4eff12c0fadc95409d5
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803809"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk-on-ios"></a>Gecomprimeerde audio-invoer van codec gebruiken met de Speech SDK op iOS

De **gecomprimeerde audio-invoer stroom** -API van de Speech SDK biedt een manier om gecomprimeerde audio naar de spraak service te streamen met behulp van een pull-of push stroom.

> [!IMPORTANT]
> Speech SDK versie 1.7.0 of hoger is vereist voor het streamen van gecomprimeerde audio op iOS. Het wordt ook ondersteund voor [ C++, C#en Java op Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9)](how-to-use-codec-compressed-audio-input-streams.md) en [java in Android.](how-to-use-codec-compressed-audio-input-streams-android.md)

Zie de mainline speech-documentatie voor WAV/PCM.  Buiten WAV/PCM worden de volgende in de codec gecomprimeerde invoer indelingen ondersteund:

- MP3
- OPUS/OGG
- FLAC
- ALAW in WAV-container
- MULAW in WAV-container

## <a name="prerequisites"></a>Vereisten

Het verwerken van gecomprimeerde audio wordt geïmplementeerd met behulp van [gstreamer](https://gstreamer.freedesktop.org).
Deze functies kunnen om licentie redenen niet worden geleverd bij de SDK, maar een wrapper-bibliotheek met deze functies moet worden gebouwd door toepassings ontwikkelaars en worden geleverd met de apps met behulp van de SDK.
Als u deze wrapper-bibliotheek wilt maken, moet u eerst de [gstreamer-SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)downloaden en installeren.
Down load vervolgens het Xcode-project voor de [wrapper-bibliotheek](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).
Open het project in Xcode en bouw het voor het **algemene IOS-apparaat** doel--het zal niet werken voor een specifiek doel.
Met de stap build wordt een dynamische Framework-bundel gegenereerd met een dynamische bibliotheek voor alle benodigde architecturen met de naam `GStreamerWrapper.framework`.
Dit framework moet worden opgenomen in alle apps die gebruikmaken van gecomprimeerde audio stromen met de speech Services SDK.

Pas de volgende instellingen in uw Xcode-project toe om dit te bewerkstelligen:

1. Kopieer zowel de `GStreamerWrapper.framework` die u zojuist hebt gemaakt en het Framework van de Cognitive Services Speech SDK, die u [hier](https://aka.ms/csspeech/iosbinary)kunt downloaden, naar de map met uw voorbeeld project.
1. Wijzig de paden in de kaders in de *project instellingen*.
    1. Op het tabblad **Algemeen**, onder de header **Ingesloten binaire bestanden** voegt u de SDK-bibliotheek toe als framework: **Inge sloten binaire bestanden toevoegen** > **andere toevoegen...** > Navigeer naar de map die u hebt gekozen en selecteer beide frameworks.
    1. Ga naar het tabblad **Build Settings** en activeer **alle** instellingen.
1. Voeg de map `$(SRCROOT)/..` toe aan de *Framework Search Paths* onder de kop **Search Paths**.

## <a name="example-code-using-codec-compressed-audio-input"></a>Voorbeeld code met behulp van gecomprimeerde audio-invoer van de codec

Als u een gecomprimeerde audio-indeling naar de spraak Services wilt streamen, maakt u een `SPXPullAudioInputStream` of `SPXPushAudioInputStream`.
Het volgende code fragment laat zien hoe u een `SPXAudioConfiguration` maakt op basis van een exemplaar van een `SPXPushAudioInputStream`, waarbij u MP3 opgeeft als de compressie-indeling van de stroom.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=66-77&highlight=2-11)]

In het volgende fragment ziet u hoe gecomprimeerde audio gegevens kunnen worden gelezen uit een bestand en worden gepompt in de `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=105-151&highlight=19-44)]

## <a name="next-steps"></a>Volgende stappen

- [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
- [Zie voor het herkennen van gesproken tekst in C#](quickstart-csharp-dotnet-windows.md)
