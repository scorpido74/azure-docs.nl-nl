---
title: 'Quickstart: Spraak herkennen vanaf een microfoon, Java (Android) - Speech-service'
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
ms.openlocfilehash: 3a3799fc1e931993c00ba497765f4cd3e60d3493
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377408"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * [Een resource voor de Azure Speech-service maken](../../../../overview.md#try-the-speech-service-for-free)
> * [De ontwikkelomgeving instellen](../../../../quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)
> * Ervoor zorgen dat u toegang tot een microfoon hebt voor het vastleggen van audio

## <a name="create-a-user-interface"></a>Een gebruikersinterface maken

Nu gaan we een eenvoudige gebruikersinterface maken voor de toepassing. Bewerk de lay-out voor de hoofdactiviteit, `activity_main.xml`. In eerste instantie bevat de indeling een titelbalk met de naam van uw toepassing en een TextView met daarin de tekst 'Hallo wereld!'.

* Selecteer het TextView-element. Wijzig het kenmerk ID in de rechterbovenhoek in `hello`.

* Sleep vanuit het palet in de linkerbovenhoek van het venster `activity_main.xml` een knop naar de lege ruimte boven de tekst.

* Ga naar de kenmerken van de knop aan de rechterkant en voer `onSpeechButtonClicked` in als de waarde voor het kenmerk `onClick`. We gaan een methode met deze naam schrijven om de knopgebeurtenis af te handelen. Wijzig het kenmerk ID in de rechterbovenhoek in `button`.

* Gebruik het pictogram van de toverstaf bovenaan de ontwerpfunctie om beperkingen voor de lay-out af te leiden.

  ![Schermafbeelding van toverstafpictogram](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-10-infer-layout-constraints.png)

De tekst en de grafische weergave van uw gebruikersinterface moeten er nu ongeveer als volgt uitzien:

![Gebruikersinterface](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-11-gui.png)

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/res/layout/activity_main.xml)]

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open het bronbestand `MainActivity.java`. Vervang alle code in het bestand door de volgende code:

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/android/from-microphone/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * De methode `onCreate` omvat code die toestemming vraagt voor gebruik van de microfoon en internet, en initialiseert de binding met het native platform. Configuratie van bindingen voor het native platform hoeft maar één keer. Dit moet gebeuren in een vroeg stadium tijdens de initialisatie van de toepassing.

   * De methode `onSpeechButtonClicked` is, zoals eerder gezegd, de methode voor het afhandelen van het klikken op de knop. Een klik op de knop betekent dat de spraak wordt omgezet in tekst.

1. Vervang in hetzelfde bestand de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang ook de tekenreeks `YourServiceRegion` door de **Regio-id** van de [regio](https://aka.ms/speech/sdkregion) die aan uw abonnement is gekoppeld.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

1. Sluit uw Android-apparaat aan op de ontwikkelmachine. Zorg ervoor dat u de [ontwikkelmodus en USB-foutopsporing](https://developer.android.com/studio/debug/dev-options) hebt ingeschakeld op het apparaat.

1. Selecteer Ctrl + F9 om de toepassing te compileren, of selecteer **Build** > **Make Project** in de menubalk.

1. Als u de toepassing wilt starten, selecteert u Shift+F10 of selecteert u **Run** > **Run 'app'** .

1. Selecteer uw Android-apparaat in het venster Deployment Target dat wordt weergegeven.

   ![Schermafbeelding van het venster Select Deployment Target](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-12-deploy.png)

Selecteer de knop in de toepassing om een sectie voor spraakherkenning te starten. De volgende 15 seconden aan Engels gesproken tekst wordt verzonden naar de Speech-service en getranscribeerd. Het resultaat wordt weergegeven in de Android-toepassing, en in het logcat-venster in Android Studio.

![Schermafbeelding van de Android-toepassing](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
