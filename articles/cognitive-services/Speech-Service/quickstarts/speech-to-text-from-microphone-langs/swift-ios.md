---
title: 'Quickstart: Spraakherkenning, Swift: Speech-service (iOS)'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een app voor spraakherkenning in Swift voor een iOS-apparaat met de Cognitive Services Speech SDK.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: chlandsi
ms.openlocfilehash: c4a66b1581049b90a419a1b62ba837fc832fd748
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512720"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Quickstart: Spraak herkennen in Swift voor iOS met behulp van de Speech SDK

Snelstarts zijn ook beschikbaar voor [spraaksynthese](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-ios.md).

In dit artikel leert u hoe u een iOS-app in Swift maakt met behulp van de Azure Cognitive Services Speech SDK om met een microfoon opgenomen spraak om te zetten in tekst.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, hebt u het volgende nodig:

* Een [abonnementssleutel](~/articles/cognitive-services/Speech-Service/get-started.md) voor de Speech-service.
* Een macOS-machine waarop [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) of hoger en [CocoaPods](https://cocoapods.org/) is geïnstalleerd.

## <a name="get-the-speech-sdk-for-ios"></a>De Speech-SDK voor iOS ophalen

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Voor deze zelfstudie kunt u geen versie van de SDK gebruiken die ouder is dan 1.6.0.

De Cognitive Services Speech SDK voor iOS wordt gedistribueerd als een frameworkbundel. De SDK kan in Xcode-projecten worden gebruikt als een [CocoaPod](https://cocoapods.org/) of worden gedownload van https://aka.ms/csspeech/iosbinary en handmatig worden gekoppeld. In dit artikel wordt gebruikgemaakt van een CocoaPod.

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Start Xcode en start een nieuw project door **File** > **New** > **Project** te selecteren.
Selecteer in het dialoogvenster voor sjabloonselectie de sjabloon **iOS Single View App**.

Maak in de dialoogvensters die volgen de volgende selecties.

1. In het dialoogvenster **Project Options**:
    1. Voer een naam in voor de snelstart-app, bijvoorbeeld *hallowereld*.
    1. Voer een relevante organisatienaam en organisatie-id in, als u al een Apple-ontwikkelaarsaccount hebt. Gebruik voor testdoeleinden een naam als *testorg*. Om de app te kunnen ondertekenen, hebt u een geschikt inrichtingsprofiel nodig. Zie de [site voor Apple-ontwikkelaars](https://developer.apple.com/) voor meer informatie.
    1. Zorg ervoor dat **Swift** als taal voor het project is gekozen.
    1. Schakel de selectievakjes uit om storyboards te gebruiken en een op documenten gebaseerde toepassing te maken. De eenvoudige gebruikersinterface voor de voorbeeld-app wordt programmatisch gemaakt.
    1. Schakel alle selectievakjes voor testen en basisgegevens uit.
1. Selecteer een projectmap:
    1. Kies een map waarin u het project wilt plaatsen. Hiermee maakt u een hallowereld-map in de gekozen map met alle bestanden voor het Xcode-project.
    1. Schakel het maken van een Git-opslagplaats uit voor dit voorbeeldproject.
1. De app moet ook het gebruik van de microfoon in het `Info.plist`-bestand declareren. Selecteer het bestand in het overzicht en voeg de sleutel **Privacy - Microphone Usage Description** toe met een waarde als *Microfoon is vereist voor spraakherkenning*.

    ![Instellingen in Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-info-plist.png)

1. Sluit het Xcode-project. Later gebruikt u een ander exemplaar van het project als u de CocoaPods hebt ingesteld.

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Plaats een nieuw header-bestand met de naam `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` in de map hallowereld in het hallowereld-project. Plak er de volgende code in:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Voeg het relatieve pad `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` naar de overbruggings-header toe aan de SWIFT-projectinstellingen voor het doel hallowereld in het veld **Objective-C Bridging Header**.

   ![Eigenschappen van header](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)

1. Vervang de inhoud van het automatisch gegenereerde `AppDelegate.swift`-bestand door de volgende code:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Vervang de inhoud van het automatisch gegenereerde `ViewController.swift`-bestand door de volgende code:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/helloworld/ViewController.swift#code)]
1. Vervang in `ViewController.swift` de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.
1. Vervang de tekenreeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld. Gebruik bijvoorbeeld `westus` voor het gratis proefabonnement.

## <a name="install-the-sdk-as-a-cocoapod"></a>De SDK installeren als een CocoaPod

1. Installeer het CocoaPod-afhankelijkheidsbeheer zoals beschreven in de [installatie-instructies](https://guides.cocoapods.org/using/getting-started.html).
1. Ga naar de map van uw voorbeeld-app, hallowereld. Plaats een tekstbestand met de naam *Podfile* en de volgende inhoud in die map:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/from-microphone/helloworld/Podfile)]
1. Ga in een Terminal naar de map hallowereld en voer opdracht `pod install` uit. Met deze opdracht wordt een `helloworld.xcworkspace`-Xcode-werkruimte gegenereerd die de voorbeeld-app, de Speech SDK en een afhankelijkheid bevat. Deze werkruimte wordt in de volgende stappen gebruikt.

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. Open de werkruimte `helloworld.xcworkspace` in Xcode.
1. Maak de foutopsporingsuitvoer zichtbaar door **View** > **Debug Area** > **Activate Console** te selecteren.
1. Kies uit de lijst in het menu **Product** > **Destination** de iOS-simulator of een iOS-apparaat dat is verbonden met uw ontwikkelcomputer als de bestemming voor de app.
1. Bouw de voorbeeldcode en voer deze uit in de iOS-simulator door **Product** > **Run** in het menu te selecteren. U kunt ook de knop **Play** selecteren.
1. Nadat u de knop **Recognize** in de app hebt geselecteerd en enkele woorden hebt gesproken, zou u de uitgesproken tekst in het onderste gedeelte van het scherm moeten kunnen zien.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Onze C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)
