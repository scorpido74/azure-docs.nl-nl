---
title: 'Quickstart: Herken spraak van een microfoon, Java (Android) - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Leer gesproken tekst herkennen in Java onder Android met behulp van de Speech SDK
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/05/2019
ms.author: wolfma
ms.openlocfilehash: 6348d09351cf627624340083e2c419def38dfc01
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77446395"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Uw ontwikkelomgeving instellen](../../../../quickstarts/setup-platform.md?tabs=android)
> * Zorg ervoor dat u toegang hebt tot een microfoon voor audio-opname

## <a name="create-a-user-interface"></a>Een gebruikersinterface maken

Nu maken we een basisgebruikersinterface voor de toepassing. Bewerk de lay-out voor de hoofdactiviteit, `activity_main.xml`. In eerste instantie bevat de lay-out een titelbalk met de naam van uw toepassing en een TextView met de tekst "Hello World!".

* Selecteer het element TextView. Wijzig het kenmerk ID in de rechterbovenhoek in `hello`.

* Sleep vanuit het palet linksboven in het `activity_main.xml` venster een knop naar de lege ruimte boven de tekst.

* Ga naar de kenmerken van de knop aan de rechterkant en voer `onSpeechButtonClicked` in als de waarde voor het kenmerk `onClick`. We gaan een methode met deze naam schrijven om de knopgebeurtenis af te handelen. Wijzig het kenmerk ID in de rechterbovenhoek in `button`.

* Gebruik het pictogram van de toverstaf bovenaan de ontwerpfunctie om beperkingen voor de lay-out af te leiden.

  ![Schermafbeelding van toverstafpictogram](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

De tekst en de grafische weergave van uw gebruikersinterface moeten er nu ongeveer als volgt uitzien:

![Gebruikersinterface](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open het bronbestand `MainActivity.java`. Vervang alle code in dit bestand door het volgende:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * De methode `onCreate` omvat code die toestemming vraagt voor gebruik van de microfoon en internet, en initialiseert de binding met het native platform. Configuratie van bindingen voor het native platform hoeft maar één keer. Dit moet gebeuren in een vroeg stadium tijdens de initialisatie van de toepassing.

   * De methode `onSpeechButtonClicked` is, zoals eerder gezegd, de methode voor het afhandelen van het klikken op de knop. Een druk op de knop activeert spraak-naar-tekst transcriptie.

1. Vervang in hetzelfde bestand de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang de `YourServiceRegion` tekenreeks ook door de **regio-id** van [regio](https://aka.ms/speech/sdkregion) die is gekoppeld aan uw abonnement. Gebruik bijvoorbeeld `westus` voor het gratis proefabonnement.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

1. Sluit uw Android-apparaat aan op de ontwikkelmachine. Zorg ervoor dat u [de ontwikkelmodus en USB-foutopsporing](https://developer.android.com/studio/debug/dev-options) op het apparaat hebt ingeschakeld.

1. Als u de toepassing wilt maken, selecteert u Ctrl+F9 of selecteert **u** > **Project maken maken** op de menubalk.

1. Als u de toepassing wilt starten, selecteert u Shift+F10 of selecteert u **'app'** **uitvoeren.** > 

1. Selecteer uw Android-apparaat in het doelvenster voor implementatie.

   ![Schermafbeelding van het venster Select Deployment Target](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Selecteer de knop in de toepassing om een sectie spraakherkenning te starten. De volgende 15 seconden aan Engels gesproken tekst wordt verzonden naar de Speech-service en getranscribeerd. Het resultaat wordt weergegeven in de Android-toepassing, en in het logcat-venster in Android Studio.

![Schermafbeelding van de Android-toepassing](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]

