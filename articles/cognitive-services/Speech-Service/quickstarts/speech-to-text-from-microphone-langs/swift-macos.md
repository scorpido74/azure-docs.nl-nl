---
title: 'Snelstart: Spraak herkennen van een microfoon, Swift - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het herkennen van spraak in Swift op macOS met de SpraakSDK
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: cbasoglu
ms.openlocfilehash: 7c5611a142087cff06eefb0277b12ff786074e1a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446832"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Snelstart: spraak herkennen in Swift op macOS met de SpraakSDK

Quickstarts zijn ook beschikbaar voor [spraaksynthese.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-macos.md)

In dit artikel leert u hoe u een macOS-app in Swift maakt met behulp van de Cognitive Services Speech SDK om spraak die is opgenomen van een microfoon naar tekst te transcriberen.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, bekijkt u de lijst vereisten:

* Een [abonnementssleutel](~/articles/cognitive-services/Speech-Service/get-started.md) voor de Spraakservice.
* Een macOS-machine met [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) of hoger en [CocoaPods](https://cocoapods.org/) geïnstalleerd.

## <a name="get-the-speech-sdk-for-macos"></a>Download de SpraakSDK voor macOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Houd er rekening mee dat deze zelfstudie niet eerder dan 1.6.0 werkt met de versie van de SDK.

De Cognitive Services Speech SDK voor macOS wordt gedistribueerd als een frameworkbundel.
Het kan worden gebruikt in Xcode projecten als https://aka.ms/csspeech/macosbinary een [CocoaPod,](https://cocoapods.org/)of gedownload van en handmatig gekoppeld. Deze gids maakt gebruik van een CocoaPod.

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Start Xcode en start een nieuw project door op**New** > Nieuw**project** **bestand** > te klikken .
Kies in het dialoogvenster sjabloonselectie de sjabloon 'Cacao-app'.

Maak in de dialoogvensters die volgen de volgende selecties:

1. Het dialoogvenster Project Options
    1. Voer een naam in voor de snelstart-app, bijvoorbeeld `helloworld`.
    1. Voer een geschikte organisatienaam en een organisatie-id in als u al een Apple-ontwikkelaarsaccount hebt. Voor testdoeleinden kunt u elke naam kiezen, zoals `testorg`. Om de app te kunnen ondertekenen, hebt u een geschikt inrichtingsprofiel nodig. Raadpleeg de [Apple-site voor ontwikkelaars](https://developer.apple.com/) voor meer informatie.
    1. Zorg ervoor dat Swift wordt gekozen als de taal voor het project.
    1. Schakel de selectievakjes uit om storyboards te gebruiken en een toepassing op basis van documenten te maken. De eenvoudige gebruikersinterface voor de voorbeeld-app wordt programmatisch gemaakt.
    1. Schakel alle selectievakjes voor tests en essentiële gegevens uit.
1. Projectmap selecteren
    1. Kies een map om het project in te plaatsen. Hiermee wordt `helloworld` een map in de gekozen map gemaakt die alle bestanden voor het Xcode-project bevat.
    1. Schakel het maken van een Git-opslagplaats uit voor dit voorbeeldproject.
1. Stel de rechten in voor toegang tot het netwerk en de microfoon. Klik op de naam van de app in de eerste regel in het overzicht aan de linkerkant om naar de app-configuratie te gaan en kies het tabblad 'Mogelijkheden'.
    1. Schakel de instelling 'Sandbox'voor de app in.
    1. Schakel de selectievakjes voor toegang tot uitgaande verbindingen en 'Microfoon' in.
    ![Sandbox-instellingen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-sandbox.png)
1. De app moet ook het gebruik `Info.plist` van de microfoon in het bestand aangeven. Klik op het bestand in het overzicht en voeg de sleutel 'Beschrijving van het gebruik van de microfoon' toe, met een waarde als 'Microfoon is nodig voor spraakherkenning'.
    ![Instellingen in Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-info-plist.png)
1. Sluit het Xcode-project. U gebruikt er later een ander exemplaar van na het instellen van de CocoaPods.

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Plaats een nieuw kopbestand `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` met `helloworld` de naam in de map in het helloworld-project en plak de volgende code erin:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Voeg het `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` relatieve pad toe aan de overbruggingskop naar de Swift-projectinstellingen voor het helloworld-doel in de eigenschappen ![ *van de overbruggingsheader van Doelstelling-C*](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-bridging-header.png)
1. Vervang de inhoud van het automatisch gegenereerde bestand `AppDelegate.swift` door:

   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Vervang `AppDelegate.swift`de tekenreeks `YourSubscriptionKey` in , vervang de tekenreeks door uw abonnementssleutel.
1. Vervang de tekenreeks `YourServiceRegion` door de regio die aan uw abonnement is gekoppeld (bijvoorbeeld `westus` voor het gratis proefabonnement).

## <a name="install-the-sdk-as-a-cocoapod"></a>Installeer de SDK als CocoaPod

1. Installeer de CocoaPod afhankelijkheidsmanager zoals beschreven in de [installatie-instructies.](https://guides.cocoapods.org/using/getting-started.html)
1. Navigeer naar de map van`helloworld`uw voorbeeld-app ( ). Plaats een tekstbestand `Podfile` met de naam en de volgende inhoud in die map:

   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/Podfile)]
1. Navigeer naar `helloworld` de map in een `pod install`terminal en voer de opdracht uit. Hierdoor wordt `helloworld.xcworkspace` een Xcode-werkruimte gegenereerd die zowel de voorbeeld-app als de Spraak-SDK als afhankelijkheid bevat. Deze werkruimte wordt als volgt gebruikt.

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. Open `helloworld.xcworkspace` de werkruimte in Xcode.
1. De foutopsporingsuitvoer zichtbaar maken **(Console** > **foutopsporingsgebied** > **weergeven).**
1. Bouw en voer de voorbeeldcode uit door **Product** > **run** te selecteren in het menu of op de knop **Afspelen** te klikken.
1. Nadat u op de knop 'Herkennen' in de app hebt geklikt en een paar woorden hebt gezegd, ziet u de tekst die u hebt gesproken in het onderste gedeelte van het app-venster.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Onze C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)

