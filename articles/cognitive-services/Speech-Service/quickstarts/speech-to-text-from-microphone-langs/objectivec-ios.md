---
title: 'Snelstart: spraak herkennen van een microfoon, Objective-C - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het herkennen van spraak in Objective-C op iOS met behulp van de Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c1246b19670a18f8dadc0c5e1c64dd5af4c1b210
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75380776"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Snelstart: spraak herkennen in Objective-C op iOS met behulp van de SpraakSDK

Quickstarts zijn ook beschikbaar voor [spraaksynthese.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-ios.md)

In dit artikel leert u hoe u een iOS-app in Doelstelling-C maakt met behulp van de Azure Cognitive Services Speech SDK om spraak te transcriberen naar tekst vanuit een microfoon of vanuit een bestand met opgenomen audio.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, hebt u het:

* Een [abonnementssleutel](~/articles/cognitive-services/Speech-Service/get-started.md) voor de Spraakservice.
* Een macOS-machine met [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) of hoger.
* Het doel is ingesteld op iOS-versie 9.3 of hoger.

## <a name="get-the-speech-sdk-for-ios"></a>De Speech-SDK voor iOS ophalen

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

De Cognitive Services Speech SDK voor iOS wordt momenteel gedistribueerd als een Cocoa framework.
Het kan worden gedownload van [deze website.](https://aka.ms/csspeech/iosbinary) Download het bestand naar uw basismap.

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Start Xcode en start een nieuw project door **Bestand** > **Nieuw** > **project te**selecteren .
Selecteer in het dialoogvenster Sjabloonselectie de sjabloon **iOS Single View App.**

Maak in de volgende dialoogvensters de volgende selecties.

1. Ga als een in het dialoogvenster **Projectopties:**
    1. Voer een naam in voor de quickstart-app, bijvoorbeeld *helloworld*.
    1. Voer een geschikte organisatienaam en organisatie-id in als u al een Apple-ontwikkelaarsaccount hebt. Gebruik voor testdoeleinden een naam als *testorg.* Om de app te kunnen ondertekenen, hebt u een geschikt inrichtingsprofiel nodig. Zie de [apple-ontwikkelaarssite](https://developer.apple.com/)voor meer informatie.
    1. Controleer of **Doelstelling-C** is geselecteerd als taal voor het project.
    1. Schakel alle selectievakjes voor tests en kerngegevens uit.

    ![Projectinstellingen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)

1. Selecteer een projectmap:
   1. Kies uw basismap waarin u het project wilt plaatsen. Met deze stap wordt een helloworld-map gemaakt in uw thuismap die alle bestanden voor het Xcode-project bevat.
   1. Schakel het maken van een Git-opslagplaats uit voor dit voorbeeldproject.
   1. Pas de paden aan de SDK aan op het scherm projectinstellingen.
      1. Voeg op het tabblad **Algemeen** onder de **koptekst Ingesloten binaire bestanden** de SDK-bibliotheek toe als framework door ingesloten binaire bestanden > toevoegen Voeg andere **toe.****Add other** Ga naar uw startmap `MicrosoftCognitiveServicesSpeech.framework`en selecteer het bestand . Met deze actie wordt de SDK-bibliotheek automatisch toegevoegd aan de header **Linked Framework en Libraries.**
         ![Toegevoegd framework](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework.png)
      1. Ga naar het tabblad **Instellingen voor bouwen** en selecteer de instelling **Alles.**
      1. Voeg de map $(SRCROOT)/.. naar **Framework Zoekpaden** onder de kop **Zoekpaden.**

      ![Instelling voor zoeken in framework](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>De gebruikersinterface instellen

De voorbeeld-app heeft een zeer eenvoudige gebruikersinterface. Het heeft twee knoppen om spraakherkenning te starten, hetzij van het bestand of van microfoon invoer en een tekstlabel om het resultaat weer te geven. De gebruikersinterface is ingesteld in het `Main.storyboard`-gedeelte van het project. Open de XML-weergave van het storyboard `Main.storyboard` door met de rechtermuisknop op de vermelding van de projectstructuur te klikken en **Open als** > **broncode te selecteren.**

Vervang de automatisch gegenereerde XML door deze code:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Download het [voorbeeldwav-bestand](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) door met de rechtermuisknop op de koppeling te klikken en **doel opslaan als te**selecteren.
   Voeg het wav-bestand als een resource toe aan het project door het vanuit een zoekvenster te verslepen naar het hoofdniveau van de projectweergave.
   Selecteer **Voltooien** in het volgende dialoogvenster zonder de instellingen te wijzigen.
1. Vervang de inhoud van `ViewController.m` het automatisch gegenereerde bestand door de volgende code:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/ViewController.m#code)]
1. Vervang de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.
1. Vervang de `YourServiceRegion` tekenreeks door het [gebied](~/articles/cognitive-services/Speech-Service/regions.md) dat aan uw abonnement is gekoppeld. Gebruik bijvoorbeeld `westus` voor het gratis proefabonnement.
1. Voeg de aanvraag voor toegang tot de microfoon toe. Klik met `Info.plist` de rechtermuisknop op de vermelding van de projectstructuur en selecteer **Openen als** > **broncode**. Voeg de volgende `<dict>` regels toe aan de sectie en sla het bestand op.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. Maak de foutopsporingsuitvoer zichtbaar door de console**Foutopsporingsgebied** >  **weergeven** > **te**selecteren .
1. Kies de iOS-simulator of een iOS-apparaat dat is verbonden met uw ontwikkelingsmachine als bestemming voor de app in de lijst in het menu > **Productbestemming.** **Product**
1. Bouw en voer de voorbeeldcode uit in de iOS-simulator door **Productrun** > in het menu**te** selecteren. U ook de **play-knop** selecteren.
1. Nadat u de knop **Herkennen (Bestand)** in de app hebt geselecteerd, ziet u de inhoud van het audiobestand 'Hoe ziet het weer?' in het onderste gedeelte van het scherm.

   ![Gesimuleerde iOS-app](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app.png)

1. Nadat u de knop **Herkennen (microfoon)** in de app hebt geselecteerd en een paar woorden hebt gezegd, ziet u de tekst die u hebt gesproken op het onderste deel van het scherm.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Objective-C-voorbeelden op GitHub verkennen](https://aka.ms/csspeech/samples)
