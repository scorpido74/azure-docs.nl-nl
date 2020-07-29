---
title: 'Quickstart: Spraak herkennen, Objective-C - Speech-services (iOS)'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een app voor spraakherkenning in Objective-C voor een iOS-apparaat met de Cognitive Services Speech SDK.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: chlandsi
ms.openlocfilehash: ba12f258ce7b0db60d3e7343bf12ffc28e8548ab
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524281"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Quickstart: Gesproken tekst herkennen in Objective-C in iOS met behulp van de Speech SDK

Snelstarts zijn ook beschikbaar voor [spraaksynthese](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-ios.md).

In dit artikel leert u hoe u een iOS-app in Objective-C maakt met behulp van de Azure Cognitive Services Speech SDK om spraak die met een microfoon is opgenomen of op een bestand staat, om te zetten in tekst.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, hebt u het volgende nodig:

* Een [abonnementssleutel](~/articles/cognitive-services/Speech-Service/get-started.md) voor de Speech-service.
* Een macOS-computer met [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) of hoger.
* Het doel is ingesteld op iOS-versie 9.3 of hoger.

## <a name="get-the-speech-sdk-for-ios"></a>De Speech-SDK voor iOS ophalen

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

De Cognitive Services Speech SDK voor iOS wordt momenteel gedistribueerd als een Cocoa-framework.
Dit kan worden gedownload van [deze website](https://aka.ms/csspeech/iosbinary). Download het bestand naar uw basismap.

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Start Xcode en start een nieuw project door **File** > **New** > **Project** te selecteren.
Selecteer in het dialoogvenster voor sjabloonselectie de sjabloon **iOS Single View App**.

Maak in de dialoogvensters die volgen de volgende selecties.

1. In het dialoogvenster **Project Options**:
    1. Voer een naam in voor de snelstart-app, bijvoorbeeld *hallowereld*.
    1. Voer een relevante organisatienaam en organisatie-id in, als u al een Apple-ontwikkelaarsaccount hebt. Gebruik voor testdoeleinden een naam als *testorg*. Om de app te kunnen ondertekenen, hebt u een geschikt inrichtingsprofiel nodig. Zie de [site voor Apple-ontwikkelaars](https://developer.apple.com/) voor meer informatie.
    1. Zorg ervoor dat **Objective-C** als de taal voor het project is gekozen.
    1. Schakel alle selectievakjes voor testen en basisgegevens uit.

    ![Projectinstellingen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)

1. Selecteer een projectmap:
   1. Kies uw basismap waarin u het project wilt plaatsen. Hiermee maakt u een hallowereld-map in de basismap met alle bestanden voor het Xcode-project.
   1. Schakel het maken van een Git-opslagplaats uit voor dit voorbeeldproject.
   1. Pas de paden naar de SDK aan in het scherm met de projectinstellingen.
      1. Voeg op het tabblad **General**, onder de kop **Embedded Binaries**, de SDK-bibliotheek toe als een framework. Selecteer hiertoe **Add embedded binaries** > **Add other**. Ga naar de basismap en selecteer het bestand `MicrosoftCognitiveServicesSpeech.framework`. Hiermee wordt de SDK-bibliotheek automatisch toegevoegd aan de kop **Linked Framework and Libraries**.
         ![Framework toegevoegd](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework.png)
      1. Ga naar het tabblad **Build Settings** en selecteer de instelling **All**.
      1. Voeg de map $(SRCROOT)/.. toe aan **Framework Search Paths** onder de kop **Search Paths**.

      ![Instelling Framework Search Paths](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>De gebruikersinterface instellen

De voorbeeld-app heeft een zeer eenvoudige gebruikersinterface. Deze heeft twee knoppen om spraakherkenning te starten vanuit bestands- of microfooninvoer en een tekstlabel om het resultaat weer te geven. De gebruikersinterface is ingesteld in het `Main.storyboard`-gedeelte van het project. Open de XML-weergave van het storyboard door met de rechtermuisknop op de vermelding `Main.storyboard` van de projectstructuur te klikken en **Open As** > **Source Code** te selecteren.

Vervang de automatisch gegenereerde XML door deze code:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Download het [wav-voorbeeldbestand](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) door met de rechtermuisknop op de koppeling te klikken en **Save target as** te kiezen.
   Voeg het wav-bestand als een resource toe aan het project door het vanuit een zoekvenster te verslepen naar het hoofdniveau van de projectweergave.
   Selecteer in het volgende dialoogvenster **Finish** zonder de instellingen te wijzigen.
1. Vervang de inhoud van het automatisch gegenereerde `ViewController.m`-bestand door de volgende code:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/ViewController.m#code)]
1. Vervang de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.
1. Vervang de tekenreeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld. Gebruik bijvoorbeeld `westus` voor het gratis proefabonnement.
1. Voeg de aanvraag voor toegang tot de microfoon toe. Klik met de rechtermuisknop op de `Info.plist`-vermelding van de projectstructuur en selecteer **Open As** > **Source Code**. Voeg de volgende regels toe in de sectie `<dict>` en sla het bestand op.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. Maak de foutopsporingsuitvoer zichtbaar door **View** > **Debug Area** > **Activate Console** te selecteren.
1. Kies uit de lijst in het menu **Product** > **Destination** de iOS-simulator of een iOS-apparaat dat is verbonden met uw ontwikkelcomputer als de bestemming voor de app.
1. Bouw de voorbeeldcode en voer deze uit in de iOS-simulator door **Product** > **Run** in het menu te selecteren. U kunt ook de knop **Play** selecteren.
1. Nadat u de knop **Recognize (File)** in de app hebt geselecteerd, ziet u de inhoud van het audiobestand 'What's the weather like?' in het onderste gedeelte van het scherm.

   ![Gesimuleerde iOS-app](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app.png)

1. Nadat u op de knop **Recognize (Microphone)** in de app hebt geklikt en enkele woorden hebt gezegd, zou u de tekst die u hebt uitgesproken moeten zien in het onderste gedeelte van het scherm.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Objective-C-voorbeelden op GitHub verkennen](https://aka.ms/csspeech/samples)
