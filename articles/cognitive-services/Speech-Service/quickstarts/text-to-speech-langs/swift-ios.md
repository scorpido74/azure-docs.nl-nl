---
title: 'Quickstart: Gesproken tekst omzetten in Swift op iOS - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Leer gesproken tekst om te zetten in Swift op iOS met behulp van de Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: yulili
ms.openlocfilehash: e71717bdacbc3c6eb08fbdc8d56ec19c26a1d114
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87062278"
---
# <a name="quickstart-synthesize-speech-in-swift-on-ios-using-the-speech-sdk"></a>Quickstart: Spraak omzetten in Swift voor iOS met behulp van de Speech SDK

In dit artikel leert u hoe u een iOS-toepassing in Swift maakt met behulp van de Cognitive Services Speech SDK om spraak om te zetten in tekst.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, bekijkt u de lijst vereisten:

* Een [abonnementssleutel](~/articles/cognitive-services/Speech-Service/get-started.md) voor de Speech-service.
* Een macOS-machine waarop [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) of hoger en [CocoaPods](https://cocoapods.org/) is geïnstalleerd.

## <a name="get-the-speech-sdk-for-ios"></a>De Speech-SDK voor iOS ophalen

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Houd er rekening mee dat deze zelfstudie niet werkt met een versie van de SDK die ouder is dan 1.7.0.

De Cognitive Services Speech SDK voor iOS wordt gedistribueerd als een frameworkbundel.
De SDK kan in Xcode-projecten worden gebruikt als een [CocoaPod](https://cocoapods.org/) of worden gedownload van https://aka.ms/csspeech/macosbinary en handmatig worden gekoppeld. In deze handleiding wordt gebruikgemaakt van een CocoaPod.

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Start Xcode en start een nieuw project starten door te klikken op **File** > **New** > **Project**.
Kies in het dialoogvenster voor het selecteren van sjablonen de sjabloon 'iOS Single View App'.

Maak in de dialoogvensters die volgen de volgende selecties:

1. Het dialoogvenster Project Options
    1. Voer een naam in voor de snelstart-app, bijvoorbeeld `helloworld`.
    1. Voer een relevante organisatienaam en organisatie-id in, als u al een Apple-ontwikkelaarsaccount hebt. Voor testdoeleinden kunt u elke naam kiezen, zoals `testorg`. Om de app te kunnen ondertekenen, hebt u een geschikt inrichtingsprofiel nodig. Raadpleeg de [Apple-site voor ontwikkelaars](https://developer.apple.com/) voor meer informatie.
    1. Zorg ervoor dat Swift als taal voor het project is gekozen.
    1. Schakel de selectievakjes uit om storyboards te gebruiken en een op documenten gebaseerde toepassing te maken. De eenvoudige gebruikersinterface voor de voorbeeld-app wordt programmatisch gemaakt.
    1. Schakel alle selectievakjes voor tests en essentiële gegevens uit.
1. Projectmap selecteren
    1. Kies een map waarin u het project wilt plaatsen. Hiermee maakt u een `helloworld`-map in de gekozen map die alle bestanden voor het Xcode-project bevat.
    1. Schakel het maken van een Git-opslagplaats uit voor dit voorbeeldproject.
1. Sluit het Xcode-project. Later gebruikt u een ander exemplaar van het project als u de CocoaPods hebt ingesteld.

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Plaats een nieuw header-bestand met de naam `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` in de map `helloworld` in het project hallowereld en plak er de volgende code in:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/text-to-speech/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Voeg het relatieve pad `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` naar de overbruggings-header toe aan de SWIFT-projectinstellingen voor het doel hallowereld in het *Objective-C Bridging Header*-veld ![Header properties](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-ios-bridging-header.png)
1. Vervang de inhoud van het automatisch gegenereerde bestand `AppDelegate.swift` door:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/text-to-speech/helloworld/helloworld/AppDelegate.swift#code)]
1. Vervang de inhoud van het automatisch gegenereerde bestand `ViewController.swift` door:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/text-to-speech/helloworld/helloworld/ViewController.swift#code)]
1. Vervang in `ViewController.swift` de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.
1. Vervang de tekenreeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld (bijvoorbeeld `westus` voor het gratis proefabonnement).

## <a name="install-the-sdk-as-a-cocoapod"></a>De SDK installeren als een CocoaPod

1. Installeer het CocoaPod-afhankelijkheidsbeheer zoals beschreven in de [installatie-instructies](https://guides.cocoapods.org/using/getting-started.html).
1. Ga naar de map van uw voorbeeld-app (`helloworld`). Plaats een tekstbestand met de naam `Podfile` en de volgende inhoud in die map:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/ios/text-to-speech/helloworld/Podfile)]
1. Ga in een terminal naar de map `helloworld` en voer opdracht `pod install` uit. Hiermee wordt een `helloworld.xcworkspace`-Xcode-werkruimte gegenereerd die de voorbeeld-app, de Speech SDK en een afhankelijkheid bevat. Deze werkruimte wordt in de volgende stappen gebruikt.

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. Open de werkruimte `helloworld.xcworkspace` in Xcode.
1. Maak de foutopsporingsuitvoer zichtbaar (**View** > **Debug Area** > **Activate Console**).
1. Kies uit de lijst in het menu **Product** > **Destination** de iOS-simulator of een iOS-apparaat dat is verbonden met uw ontwikkelcomputer als de bestemming voor de app.
1. Bouw de voorbeeldcode en voer deze uit in de iOS-simulator door **Product** > **Run** in het menu te selecteren of op de knop **Play** te klikken.
1. Nadat u tekst hebt ingevoerd en op de knop in de app hebt geklikt, moet u horen dat de geteste audio wordt afgespeeld.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Onze C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)
