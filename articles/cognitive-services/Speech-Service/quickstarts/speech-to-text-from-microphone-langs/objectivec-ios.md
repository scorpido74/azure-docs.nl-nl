---
title: 'Quick Start: spraak herkennen vanuit een microfoon, doel-C-spraak service'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het herkennen van spraak in doel-C op iOS met behulp van de Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 0d353b115acbf48bfffa6c2033b507721de501c5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503617"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-by-using-the-speech-sdk"></a>Snelstartgids: spraak in doel-C op iOS herkennen met de Speech SDK

Quick starts zijn ook beschikbaar voor [spraak synthese](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-ios.md).

In dit artikel leert u hoe u een iOS-app maakt in doel-C met behulp van de Azure Cognitive Services Speech SDK om spraak naar tekst van een microfoon of van een bestand met opgenomen audio te transcriberen.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, hebt u het volgende nodig:

* Een [abonnements sleutel](~/articles/cognitive-services/Speech-Service/get-started.md) voor de spraak service.
* Een macOS-computer met [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) of hoger.
* De doel ingesteld op iOS-versie 9,3 of hoger.

## <a name="get-the-speech-sdk-for-ios"></a>De Speech-SDK voor iOS ophalen

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

De Cognitive Services Speech SDK voor iOS wordt momenteel gedistribueerd als een cacao-Framework.
Dit kan vanaf [deze website](https://aka.ms/csspeech/iosbinary)worden gedownload. Download het bestand naar uw basismap.

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Start Xcode en start een nieuw project door **bestand** > **Nieuw** > **project**te selecteren.
Selecteer in het dialoog venster sjabloon selectie de sjabloon **voor de IOS-app met één weer gave** .

In de volgende dialoog vensters volgt u de onderstaande opties.

1. In het dialoog venster **project opties** :
    1. Voer een naam in voor de Quick Start-app, bijvoorbeeld *HelloWorld*.
    1. Voer de juiste organisatie naam en organisatie-id in als u al een Apple-ontwikkelaars account hebt. Gebruik voor test doeleinden een naam zoals *testorg*. Om de app te kunnen ondertekenen, hebt u een geschikt inrichtingsprofiel nodig. Zie de [Apple Developer-site](https://developer.apple.com/)voor meer informatie.
    1. Zorg ervoor dat de **doel-C** is geselecteerd als de taal voor het project.
    1. Schakel alle selectie vakjes voor testen en basis gegevens uit.

    ![Project instellingen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-project-settings.png)

1. Selecteer een projectmap:
   1. Kies uw basismap waarin u het project wilt plaatsen. Met deze stap maakt u een map HelloWorld in uw basismap die alle bestanden voor het Xcode-project bevat.
   1. Schakel het maken van een Git-opslagplaats uit voor dit voorbeeldproject.
   1. Pas de paden aan op het scherm project instellingen van de SDK.
      1. Voeg op het tabblad **Algemeen** onder de kop **Inge sloten binaire bestanden** de SDK-bibliotheek toe als een Framework door **inge sloten binaire bestanden toevoegen** > **andere toevoegen**toe te voegen. Ga naar de basismap en selecteer het bestand `MicrosoftCognitiveServicesSpeech.framework`. Met deze actie wordt de SDK-bibliotheek automatisch toegevoegd aan de koptekst van het **gekoppelde Framework en de bibliotheken** .
         ![toegevoegde Framework](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework.png)
      1. Ga naar het tabblad **instellingen voor samen stellen** en selecteer de instelling **alle** .
      1. Voeg de map $ (SRCROOT)/toe. naar **Framework Zoek paden** onder de kop **Zoek paden** .

      ![Instelling voor Framework-Zoek paden](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>De gebruikersinterface instellen

De voor beeld-app heeft een zeer eenvoudige gebruikers interface. Het heeft twee knoppen om spraak herkenning te starten vanuit een bestand of van een microfoon invoer en een tekst label om het resultaat weer te geven. De gebruikersinterface is ingesteld in het `Main.storyboard`-gedeelte van het project. Open de XML-weer gave van het Story Board door met de rechter muisknop op de `Main.storyboard` vermelding van de project structuur te klikken en **vervolgens openen als** > - **bron code**te selecteren.

Vervang de automatisch gegenereerde XML door deze code:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Down load het [WAV-voorbeeld bestand](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) door met de rechter muisknop op de koppeling te klikken en **doel opslaan als**te selecteren.
   Voeg het wav-bestand als een resource toe aan het project door het vanuit een zoekvenster te verslepen naar het hoofdniveau van de projectweergave.
   Selecteer in het volgende dialoog venster **volt ooien** zonder de instellingen te wijzigen.
1. Vervang de inhoud van het automatisch gegenereerde `ViewController.m` bestand door de volgende code:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/ios/from-microphone/helloworld/helloworld/ViewController.m#code)]
1. Vervang de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.
1. Vervang de teken reeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld. Gebruik bijvoorbeeld `westus` voor het gratis proef abonnement.
1. Voeg de aanvraag voor toegang tot de microfoon toe. Klik met de rechter muisknop op de `Info.plist` vermelding van de project structuur en selecteer **openen als** > - **bron code**. Voeg de volgende regels toe aan de sectie `<dict>` en sla het bestand op.

    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. Maak de uitvoer van de fout opsporing zichtbaar door > **gebied voor fout opsporing** **weer geven** > **console activeren**te selecteren.
1. Kies de iOS-Simulator of een iOS-apparaat dat is verbonden met uw ontwikkel computer als bestemming voor de app uit de lijst in het menu **Product** > **doel** .
1. Bouw en voer de voorbeeld code in de iOS-Simulator door in het menu **Product** > uit te **voeren** . U kunt ook de knop **afspelen** selecteren.
1. Nadat u de knop **herkennen (bestand)** in de app hebt geselecteerd, wordt de inhoud van het audio bestand weer geven ' wat is het weer? ' in het onderste gedeelte van het scherm.

   ![Gesimuleerde iOS-app](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-simulated-app.png)

1. Nadat u de knop **herkennen (microfoon)** in de app hebt geselecteerd en enkele woorden hebt gedicteerd, ziet u de tekst die u hebt gesp roken op het onderste deel van het scherm.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Objective-C-voorbeelden op GitHub verkennen](https://aka.ms/csspeech/samples)
