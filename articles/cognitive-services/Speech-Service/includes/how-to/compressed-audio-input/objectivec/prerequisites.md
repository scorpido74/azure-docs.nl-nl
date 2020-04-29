---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 7106e139108681e1908b20d2daac5e619a63555d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421828"
---
Het verwerken van gecomprimeerde audio wordt geïmplementeerd met behulp van [gstreamer](https://gstreamer.freedesktop.org). Om licentie redenen GStreamer binaire bestanden niet worden gecompileerd en gekoppeld aan de spraak-SDK. In plaats daarvan moet een wrapper-bibliotheek met deze functies worden gebouwd en geleverd met de apps met behulp van de SDK.

Als u deze wrapper-bibliotheek wilt maken, moet u eerst de [gstreamer-SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)downloaden en installeren. Down load vervolgens het **Xcode** -project voor de [wrapper-bibliotheek](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Open het project in **Xcode** en bouw het voor het **algemene IOS-apparaat** doel--het zal *niet* werken voor een specifiek doel.

Met de stap build wordt een dynamische Framework-bundel gegenereerd met een dynamische bibliotheek voor alle benodigde architecturen met de `GStreamerWrapper.framework`naam van.

Dit framework moet worden opgenomen in alle apps die gebruikmaken van gecomprimeerde audio stromen met de Speech Service SDK.

Pas de volgende instellingen in uw **Xcode** -project toe om dit te bewerkstelligen:

1. Kopieer de `GStreamerWrapper.framework` zojuist gebouwde en het Framework van de COGNITIVE Services Speech SDK, die u [hier](https://aka.ms/csspeech/iosbinary)kunt downloaden, naar de map met uw voorbeeld project.
1. Wijzig de paden in de kaders in de *project instellingen*.
   1. Voeg op het tabblad **Algemeen** onder de kop **Inge sloten binaire bestanden** de SDK-bibliotheek toe als Framework: Voeg **Inge sloten binaire bestanden** > toe **...** > gaat u naar de map die u hebt gekozen en selecteert u beide frameworks.
   1. Ga naar het tabblad **Build Settings** en activeer **alle** instellingen.
1. Voeg de map `$(SRCROOT)/..` toe aan de _Framework Search Paths_ onder de kop **Search Paths**.
