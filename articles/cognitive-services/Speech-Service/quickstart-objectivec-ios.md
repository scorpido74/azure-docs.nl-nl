---
title: 'Quickstart: Spraak, doel-C-spraak service herkennen'
titleSuffix: Azure Cognitive Services
description: Informatie over het herkennen van gesproken tekst in Objective-C in iOS met behulp van de Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: d87fd06b45d1d3636c442d962daa79cf526e74f2
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803270"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Quickstart: Gesproken tekst herkennen in Objective-C in iOS met behulp van de Speech SDK

Quick starts zijn ook beschikbaar voor [spraak synthese](quickstart-text-to-speech-objectivec-ios.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In dit artikel leert u hoe u een iOS-app maakt in doel-C met behulp van de SDK van Cognitive Services speech om spraak naar tekst van een microfoon of van een bestand met opgenomen audio te transcriberen.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, bekijkt u de lijst vereisten:

* Een [abonnementssleutel](get-started.md) voor Speech Service
* Een macOS-computer met [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) of hoger
* De doel ingesteld op iOS-versie 9,3 of hoger

## <a name="get-the-speech-sdk-for-ios"></a>De Speech-SDK voor iOS ophalen

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

De Cognitive Services Speech SDK voor iOS wordt momenteel gedistribueerd als een cacao-Framework.
Dit kan [hier](https://aka.ms/csspeech/iosbinary)worden gedownload. Download het bestand naar uw basismap.

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Start Xcode en start een nieuw project starten door te klikken op **File** > **New** > **Project**.
Kies in het dialoogvenster voor het selecteren van sjablonen de sjabloon 'iOS Single View App'.

Maak in de dialoogvensters die volgen de volgende selecties:

1. Het dialoogvenster Project Options
    1. Voer een naam in voor de snelstart-app, bijvoorbeeld `helloworld`.
    1. Voer een relevante organisatienaam en organisatie-id in, als u al een Apple-ontwikkelaarsaccount hebt. Voor testdoeleinden kunt u elke naam kiezen, zoals `testorg`. Om de app te kunnen ondertekenen, hebt u een geschikt inrichtingsprofiel nodig. Raadpleeg de [Apple-site voor ontwikkelaars](https://developer.apple.com/) voor meer informatie.
    1. Zorg ervoor dat Objective-C als de taal voor het project is gekozen.
    1. Schakel alle selectievakjes voor tests en essentiële gegevens uit.
    ![Projectinstellingen](media/sdk/qs-objectivec-project-settings.png)
1. Projectmap selecteren
    1. Kies uw basismap waarin u het project wilt plaatsen. Hiermee maakt u een `helloworld`-map in uw basismap die alle bestanden voor het Xcode-project bevat.
    1. Schakel het maken van een Git-opslagplaats uit voor dit voorbeeldproject.
    1. Pas de paden naar de SDK aan in de *projectinstellingen*.
        1. Op het tabblad **Algemeen**, onder de header **Ingesloten binaire bestanden** voegt u de SDK-bibliotheek toe als framework: **Ingesloten binaire bestanden toevoegen** > **Overige toevoegen...** > Navigeer naar uw basismap en kies het bestand `MicrosoftCognitiveServicesSpeech.framework`. Hiermee wordt de SDK-bibliotheek automatisch toegevoegd aan de kop **Linked Framework and Libraries**.
        ![Framework toegevoegd](media/sdk/qs-objectivec-framework.png)
        1. Ga naar het tabblad **Build Settings** en activeer **alle** instellingen.
        1. Voeg de map `$(SRCROOT)/..` toe aan de *Framework Search Paths* onder de kop **Search Paths**.
        ![Instelling voor Framework Search Paths](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>De gebruikersinterface instellen

De voorbeeldapp heeft een zeer eenvoudige gebruikersinterface: twee knoppen om spraakherkenning te starten vanuit bestands- of microfooninvoer en een tekstlabel om het resultaat weer te geven.
De gebruikersinterface is ingesteld in het `Main.storyboard`-gedeelte van het project.
Open de XML-weergave van het storyboard door met de rechtermuisknop op de vermelding `Main.storyboard` van de projectstructuur te klikken en **Open As...**  > **Source Code** te selecteren.
Vervang de automatisch gegenereerde XML door deze code:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Download het [wav-voorbeeldbestand](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) door met de rechtermuisknop op de koppeling te klikken en **Save target as...** te kiezen. Voeg het wav-bestand als een resource toe aan het project door het vanuit een zoekvenster te verslepen naar het hoofdniveau van de projectweergave.
   Klik in het volgende dialoogvenster op **Finish** zonder de instellingen te wijzigen.
1. Vervang de inhoud van het automatisch gegenereerde bestand `ViewController.m` door:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Vervang de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.
1. Vervang de tekenreeks `YourServiceRegion` door de [regio](regions.md) die aan uw abonnement is gekoppeld (bijvoorbeeld `westus` voor het gratis proefabonnement).
1. Voeg de aanvraag voor toegang tot de microfoon toe. Klik met de rechtermuisknop op de `Info.plist`-vermelding van de projectstructuur en selecteer **Open As...**  > **Source Code**. Voeg de volgende regels toe in de sectie `<dict>` en sla het bestand op.
    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. Maak de foutopsporingsuitvoer zichtbaar (**View** > **Debug Area** > **Activate Console**).
1. Kies de iOS-Simulator of een iOS-apparaat dat is verbonden met uw ontwikkel computer als bestemming voor de app uit de lijst in het**doel** menu **product** > .
1. Bouw de voorbeeldcode en voer deze uit in de iOS-simulator door **Product** > **Run** in het menu te selecteren of op de knop **Play** te klikken.
1. Nadat u op de knop 'Recognize (File)' in de app hebt geklikt, ziet u de inhoud van het audiobestand 'What's the weather like?' in het onderste gedeelte van het scherm.

   ![Gesimuleerde iOS-app](media/sdk/qs-objectivec-simulated-app.png)

1. Nadat u op de knop 'Recognize (Microphone)' in de app hebt geklikt en enkele woorden hebt gezegd, zou u de tekst die u hebt uitgesproken moeten zien in het onderste gedeelte van het scherm.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Objective-C-voorbeelden op GitHub verkennen](https://aka.ms/csspeech/samples)
