---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 28f6ef7248fada8286bbe90c868cd9f947f0435d
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943792"
---
Het verwerken van gecomprimeerde audio wordt geïmplementeerd met behulp van [gstreamer](https://gstreamer.freedesktop.org). Om licentie redenen GStreamer binaire bestanden niet worden gecompileerd en gekoppeld aan de spraak-SDK. In plaats daarvan moet een wrapper-bibliotheek met deze functies worden gebouwd en geleverd met de apps met behulp van de SDK.

Als u deze wrapper-bibliotheek wilt maken, moet u eerst de [gstreamer-SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg)downloaden en installeren. Down load vervolgens het **Xcode** -project voor de [wrapper-bibliotheek](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Open het project in **Xcode** en bouw het voor het **algemene IOS-apparaat** doel--het zal *niet* werken voor een specifiek doel.

Met de stap build wordt een dynamisch Framework gegenereerd met een dynamische bibliotheek voor alle benodigde architecturen met de naam van `GStreamerWrapper.framework`.

Dit framework moet worden opgenomen in alle apps die gebruikmaken van gecomprimeerde audio stromen met de Speech Service SDK.

Pas de volgende instellingen in uw **Xcode** -project toe om dit te bewerkstelligen:

1. Kopieer de `GStreamerWrapper.framework` die u zojuist hebt gemaakt en het Framework van de Cognitive Services Speech SDK, die u [hier](https://aka.ms/csspeech/iosbinary)kunt downloaden, naar de map met uw voorbeeld project.
1. Wijzig de paden in de kaders in de *project instellingen*.
   1. Voeg op het tabblad **Algemeen** onder de kop **Inge sloten binaire bestanden** de SDK-bibliotheek toe als Framework: **Voeg inge sloten binaire bestanden** toe > **Voeg andere toe...** > Navigeer naar de map die u hebt gekozen en selecteer beide frameworks.
   1. Ga naar het tabblad **Build Settings** en activeer **alle** instellingen.
1. Voeg de map `$(SRCROOT)/..` toe aan de _Framework Search Paths_ onder de kop **Search Paths**.
