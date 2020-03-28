---
title: 'Snelstart: Spraak herkennen van een microfoon, Swift - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het herkennen van spraak in Swift op iOS met de Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: 31f5339c70d52019400ca5f1fe873de4790a3bd6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75380521"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Snelstart: spraak herkennen in Swift op iOS met behulp van de Speech SDK

Quickstarts zijn ook beschikbaar voor [spraaksynthese.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-ios.md)

In dit artikel leert u hoe u een iOS-app in Swift maakt met behulp van de Azure Cognitive Services Speech SDK om spraak die is opgenomen van een microfoon naar tekst te transcriberen.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, hebt u het:

* Een [abonnementssleutel](~/articles/cognitive-services/Speech-Service/get-started.md) voor de Spraakservice.
* Een macOS-machine met [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) of hoger en [CocoaPods](https://cocoapods.org/) geïnstalleerd.

## <a name="get-the-speech-sdk-for-ios"></a>De Speech-SDK voor iOS ophalen

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Deze zelfstudie werkt niet eerder dan 1.6.0 met een versie van de SDK.

De Cognitive Services Speech SDK voor iOS wordt gedistribueerd als een frameworkbundel. Het kan worden gebruikt in Xcode projecten als https://aka.ms/csspeech/iosbinary een [CocoaPod](https://cocoapods.org/) of gedownload van en handmatig gekoppeld. Dit artikel maakt gebruik van een CocoaPod.

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Start Xcode en start een nieuw project door **Bestand** > **Nieuw** > **project te**selecteren .
Selecteer in het dialoogvenster Sjabloonselectie de sjabloon **iOS Single View App.**

Maak in de volgende dialoogvensters de volgende selecties.

1. Ga als een in het dialoogvenster **Projectopties:**
    1. Voer een naam in voor de quickstart-app, bijvoorbeeld *helloworld*.
    1. Voer een geschikte organisatienaam en een organisatie-id in als u al een Apple-ontwikkelaarsaccount hebt. Gebruik voor testdoeleinden een naam als *testorg.* Om de app te kunnen ondertekenen, hebt u een geschikt inrichtingsprofiel nodig. Zie de [apple-ontwikkelaarssite](https://developer.apple.com/)voor meer informatie.
    1. Zorg ervoor dat **Swift** wordt gekozen als de taal voor het project.
    1. Schakel de selectievakjes uit om storyboards te gebruiken en een toepassing op basis van documenten te maken. De eenvoudige gebruikersinterface voor de voorbeeld-app wordt programmatisch gemaakt.
    1. Schakel alle selectievakjes voor tests en kerngegevens uit.
1. Selecteer een projectmap:
    1. Kies een map om het project in te plaatsen. Met deze stap wordt een helloworld-map gemaakt in de gekozen map die alle bestanden voor het Xcode-project bevat.
    1. Schakel het maken van een Git-opslagplaats uit voor dit voorbeeldproject.
1. De app moet ook het gebruik `Info.plist` van de microfoon in het bestand aangeven. Selecteer het bestand in het overzicht en voeg de sleutel **Privacy - Beschrijvingvan het microfoongebruik** toe met een waarde zoals *microfoon is vereist voor spraakherkenning.*

    ![Instellingen in Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-info-plist.png)

1. Sluit het Xcode-project. U gebruikt een ander exemplaar van het later nadat u de CocoaPods hebt ingesteld.

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Plaats een nieuw headerbestand `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` met de naam in de helloworld-map in het helloworld-project. Plak de volgende code erin:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Voeg het `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` relatieve pad toe aan de overbruggingskop naar de Swift-projectinstellingen voor het helloworld-doel in het veld **Overbruggingsheader doelstelling-C.**

   ![Eigenschappen voor kopteksten](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)

1. Vervang de inhoud van `AppDelegate.swift` het automatisch gegenereerde bestand door de volgende code:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Vervang de inhoud van `ViewController.swift` het automatisch gegenereerde bestand door de volgende code:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/ViewController.swift#code)]
1. Vervang `ViewController.swift`de tekenreeks `YourSubscriptionKey` in , vervang de tekenreeks door uw abonnementssleutel.
1. Vervang de `YourServiceRegion` tekenreeks door het [gebied](~/articles/cognitive-services/Speech-Service/regions.md) dat aan uw abonnement is gekoppeld. Gebruik bijvoorbeeld `westus` voor het gratis proefabonnement.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installeer de SDK als CocoaPod

1. Installeer de CocoaPod afhankelijkheidsmanager zoals beschreven in de [installatie-instructies.](https://guides.cocoapods.org/using/getting-started.html)
1. Ga naar de directory van uw voorbeeld app, dat is helloworld. Plaats een tekstbestand met de naam *Podfile* en de volgende inhoud in die map:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/Podfile)]
1. Ga naar de helloworld directory in een `pod install`terminal en voer de opdracht uit. Met deze `helloworld.xcworkspace` opdracht genereert u een Xcode-werkruimte die zowel de voorbeeld-app als de Spraak-SDK als afhankelijkheid bevat. Deze werkruimte wordt gebruikt in de volgende stappen.

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. Open de `helloworld.xcworkspace` werkruimte in Xcode.
1. Maak de foutopsporingsuitvoer zichtbaar door de console**Foutopsporingsgebied** >  **weergeven** > **te**selecteren .
1. Kies de iOS-simulator of een iOS-apparaat dat is verbonden met uw ontwikkelingsmachine als bestemming voor de app in de lijst in het menu > **Productbestemming.** **Product**
1. Bouw en voer de voorbeeldcode uit in de iOS-simulator door **Productrun** > in het menu**te** selecteren. U ook de **play-knop** selecteren.
1. Nadat u de knop **Herkennen** in de app hebt geselecteerd en een paar woorden hebt gezegd, ziet u de tekst die u hebt gesproken op het onderste deel van het scherm.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Onze C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)
