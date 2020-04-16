---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 7106e139108681e1908b20d2daac5e619a63555d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421828"
---
Het verwerken van gecomprimeerde audio wordt geïmplementeerd met [GStreamer](https://gstreamer.freedesktop.org). Om licentieredenen worden GStreamer-binaries niet gecompileerd en gekoppeld aan de Speech SDK. In plaats daarvan moet een wrapper-bibliotheek met deze functies worden gebouwd en verzonden met de apps die de SDK gebruiken.

Om deze wrapper bibliotheek te bouwen, eerst downloaden en installeren van de [GStreamer SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Download vervolgens het **Xcode-project** voor de [wrapper-bibliotheek.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper)

Open het project in **Xcode** en bouw het voor het **algemene iOS-apparaatdoel** - het zal *niet* werken om het te bouwen voor een specifiek doel.

De buildstap genereert een dynamische frameworkbundel met een dynamische bibliotheek `GStreamerWrapper.framework`voor alle benodigde architecturen met de naam .

Dit framework moet worden opgenomen in alle apps die gecomprimeerde audiostreams gebruiken met de Spraakservice SDK.

Pas de volgende instellingen in uw **Xcode-project** toe om dit te bereiken:

1. Kopieer `GStreamerWrapper.framework` de zojuist gebouwde u en het kader van de Cognitive Services Speech SDK, die u vanaf [hier](https://aka.ms/csspeech/iosbinary)downloaden naar de directory met uw voorbeeldproject.
1. Pas de paden aan de frameworks in de *projectinstellingen aan.*
   1. Voeg op het tabblad **Algemeen** onder de **koptekst Ingesloten binaire bestanden** de SDK-bibliotheek toe als framework: **Ingesloten binaire bestanden** > toevoegen**Voeg andere...** > Navigeer naar de lijst die u hebt gekozen en selecteer beide frameworks.
   1. Ga naar het tabblad **Build Settings** en activeer **alle** instellingen.
1. Voeg de map `$(SRCROOT)/..` toe aan de _Framework Search Paths_ onder de kop **Search Paths**.
