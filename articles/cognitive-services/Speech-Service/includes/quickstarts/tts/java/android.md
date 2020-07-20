---
title: 'Quickstart: Spraak omzetten, Java (Android) - Speech-service'
titleSuffix: Azure Cognitive Services
description: Leer gesproken tekst om te zetten in Java onder Android met behulp van de Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: yulili
ms.openlocfilehash: 8f3a6c808cec28d3c2184bfd99aa2f5b1f6f88a3
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86226419"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een resource voor de Azure Speech-service maken](../../../../get-started.md)
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)

## <a name="create-user-interface"></a>Gebruikersinterface maken

We gaan een eenvoudige gebruikersinterface maken voor de toepassing. Bewerk de lay-out voor de hoofdactiviteit, `activity_main.xml`. In eerste instantie bevat de lay-out een titelbalk met de naam van uw toepassing en een TextView met de tekst "Hello World!".

1. Klik op het TextView-element. Wijzig het kenmerk ID in de rechterbovenhoek in `outputMessage` en sleep het naar het onderste scherm. Verwijder de tekst.

1. Sleep vanuit het palet in de linkerbovenhoek van het venster `activity_main.xml` een knop naar de lege ruimte boven de tekst.

1. Ga naar de kenmerken van de knop aan de rechterkant en voer `onSpeechButtonClicked` in als de waarde voor het kenmerk `onClick`. We gaan een methode met deze naam schrijven om de knopgebeurtenis af te handelen.  Wijzig het kenmerk ID in de rechterbovenhoek in `button`.

1. Sleep een tekst zonder opmaak naar de ruimte boven de knop. Verander het kenmerk ID naar `speakText` en het tektskenmerk naar `Hi there!`.

1. Gebruik het pictogram van de toverstaf bovenaan de ontwerpfunctie om beperkingen voor de lay-out af te leiden.


    ![Schermafbeelding van toverstafpictogram](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

De tekst en de grafische weergave van uw gebruikersinterface moeten er nu ongeveer als volgt uitzien:

![Schermopname van hoe uw gebruikersinterface er zou moeten uitzien.](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open het bronbestand `MainActivity.java`. Vervang alle code in het bestand door de volgende code.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/text-to-speech/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * De methode `onSpeechButtonClicked` is, zoals eerder gezegd, de methode voor het afhandelen van het klikken op de knop. U activeert spraaksynthese met een druk op de knop.

1. Vervang in hetzelfde bestand de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang ook de tekenreeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld (bijvoorbeeld `westus` voor het gratis proefabonnement).

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

1. Sluit uw Android-apparaat aan op de ontwikkelmachine. Zorg ervoor dat u de [ontwikkelmodus en USB-foutopsporing](https://developer.android.com/studio/debug/dev-options) hebt ingeschakeld op het apparaat. U kunt ook een [Android-emulator](https://developer.android.com/studio/run/emulator) maken.

1. Druk op Ctrl + F9 om de toepassing te compileren, of kies **Build** > **Make Project** in de menubalk.

1. Als u de toepassing wilt starten, drukt u op Shift + F10 of kiest u **Run** > **Run 'app'** .

1. Kies uw Android-apparaat of -emulator in het venster Select Deployment Target dat wordt weergegeven.

   ![Schermafbeelding van het venster Select Deployment Target](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Voer een tekst in en klik op de knop in de toepassing om een sectie voor spraaksynthese te starten. U hoort de audiosynthese door de standaard luidspreker en ziet de `speech synthesis succeeded`-informatie op het scherm.

![Schermafbeelding van de Android-toepassing](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Zie ook

- [Een Custom Voice maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste stemvoorbeelden opnemen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
