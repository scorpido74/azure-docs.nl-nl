---
title: 'Quickstart: Upfunctie speech, java (Android)-Speech Service'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het op Android gebruiken van spraak in Java met behulp van de Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/19/2019
ms.author: yulili
ms.openlocfilehash: 17fb0c1a4ba5495506ceb658eb61d0ac2959caf8
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71804039"
---
# <a name="quickstart-synthesize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Quickstart: Spraak in Java op Android met behulp van de Speech SDK

Quick starts zijn ook beschikbaar voor [spraak herkenning](quickstart-java-android.md) en de [eerste virtuele assistent](quickstart-virtual-assistant-java-android.md).

In dit artikel leert u hoe u een Java-toepassing voor Android kunt ontwikkelen met behulp van de SDK van Cognitive Services speech om spraak vanuit tekst te maken.
De toepassing is gebaseerd op het Speech SDK maven-pakket, versie 1.7.0 en Android Studio 3,3.
De Speech SDK is op dit moment compatibel met Android-apparaten met 32/64-bits ARM- en Intel x86/x64-compatibele processors.

> [!NOTE]
> Zie [Speech Devices SDK](speech-devices-sdk.md) voor de Speech Devices-SDK en het Roobo-apparaat.

## <a name="prerequisites"></a>Vereisten

U hebt een abonnement op spraak services nodig om deze Quick Start te volt ooien. U kunt er gratis een krijgen. Zie [de spraak services gratis uitproberen](get-started.md) voor meer informatie.

## <a name="create-and-configure-a-project"></a>Een project maken en configureren

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Gebruikersinterface maken

We gaan een eenvoudige gebruikersinterface maken voor de toepassing. Bewerk de lay-out voor de hoofdactiviteit, `activity_main.xml`. In eerste instantie bevat de lay-out een titelbalk met de naam van uw toepassing en een TextView met de tekst "Hello World!".

1. Klik op het TextView-element. Wijzig het kenmerk ID in de rechter bovenhoek naar `outputMessage` en sleep het naar het onderste scherm. De tekst verwijderen.

1. Sleep vanuit het palet in de linkerbovenhoek van het venster `activity_main.xml` een knop naar de lege ruimte boven de tekst.

1. Ga naar de kenmerken van de knop aan de rechterkant en voer `onSpeechButtonClicked` in als de waarde voor het kenmerk `onClick`. We gaan een methode met deze naam schrijven om de knopgebeurtenis af te handelen.  Wijzig het kenmerk ID in de rechterbovenhoek in `button`.

1. Sleep een tekst zonder opmaak naar de ruimte boven de knop. Wijzig het kenmerk ID in `speakText` en wijzig het tekst kenmerk in `Hi there!`.

1. Gebruik het pictogram van de toverstaf bovenaan de ontwerpfunctie om beperkingen voor de lay-out af te leiden.


    ![Schermafbeelding van toverstafpictogram](media/sdk/qs-java-android-10-infer-layout-constraints.png)

De tekst en de grafische weergave van uw gebruikersinterface moeten er nu ongeveer als volgt uitzien:

![](media/sdk/qs-java-android-11-2-tts-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open het bronbestand `MainActivity.java`. Vervang alle code in het bestand door de volgende code.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * De methode `onSpeechButtonClicked` is, zoals eerder gezegd, de methode voor het afhandelen van het klikken op de knop. Met een knop-Klik wordt spraak-synthese geactiveerd.

1. Vervang in hetzelfde bestand de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang ook de tekenreeks `YourServiceRegion` door de [regio](regions.md) die aan uw abonnement is gekoppeld (bijvoorbeeld `westus` voor het gratis proefabonnement).

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

1. Sluit uw Android-apparaat aan op de ontwikkelmachine. Zorg ervoor dat u de [ontwikkelmodus en USB-foutopsporing](https://developer.android.com/studio/debug/dev-options) hebt ingeschakeld op het apparaat. U kunt ook een [Android-Emulator](https://developer.android.com/studio/run/emulator)maken.

1. Druk op Ctrl + F9 om de toepassing te compileren, of kies **Build** > **Make Project** in de menubalk.

1. Als u de toepassing wilt starten, drukt u op Shift + F10 of kiest u **Run** > **Run 'app'** .

1. Kies in het venster implementatie doel dat wordt weer gegeven uw Android-apparaat of-emulator.

   ![Schermafbeelding van het venster Select Deployment Target](media/sdk/qs-java-android-12-deploy.png)

Voer een tekst in en druk op de knop in de toepassing om een sectie met spraak synthese te starten. U hoort de gesynthesizerde audio van de standaard spreker en ziet de `speech synthesis succeeded`-informatie op het scherm.

![Schermafbeelding van de Android-toepassing](media/sdk/qs-java-android-13-2-gui-on-device-tts.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bekijk Java-voorbeelden op GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zie ook

- [Spraak lettertypen aanpassen](how-to-customize-voice-font.md)
- [Spraak voorbeelden vastleggen](record-custom-voice-samples.md)
