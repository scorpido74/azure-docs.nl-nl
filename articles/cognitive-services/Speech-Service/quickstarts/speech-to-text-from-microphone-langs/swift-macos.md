---
title: 'Quickstart: Spraakherkenning, Swift: Speech-service (macOS)'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een app voor spraakherkenning in SWIFT voor een macOS-computer met de Cognitive Services Speech SDK.
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: cbasoglu
ms.openlocfilehash: 68947446d18ce1632b63ac3ba27311f648f3deea
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524108"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Quickstart: Spraak herkennen in Swift voor macOS met behulp van de Speech SDK

Snelstarts zijn ook beschikbaar voor [spraaksynthese](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/swift-macos.md).

In dit artikel leert u hoe u een macOS-toepassing in Swift maakt met behulp van de Cognitive Services Speech SDK om met een microfoon opgenomen spraak om te zetten in tekst.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, bekijkt u de lijst vereisten:

* Een [abonnementssleutel](~/articles/cognitive-services/Speech-Service/get-started.md) voor de Speech-service.
* Een macOS-machine waarop [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) of hoger en [CocoaPods](https://cocoapods.org/) is geïnstalleerd.

## <a name="get-the-speech-sdk-for-macos"></a>De Speech SDK voor macOS ophalen

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Houd er rekening mee dat deze zelfstudie niet werkt met een versie van de SDK die ouder is dan 1.6.0.

De Cognitive Services Speech SDK voor macOS wordt gedistribueerd als een frameworkbundel.
De SDK kan in Xcode-projecten worden gebruikt als een [CocoaPod](https://cocoapods.org/) of worden gedownload van https://aka.ms/csspeech/macosbinary en handmatig worden gekoppeld. In deze handleiding wordt gebruikgemaakt van een CocoaPod.

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Start Xcode en start een nieuw project starten door te klikken op **File** > **New** > **Project**.
Kies in het dialoogvenster voor het selecteren van sjablonen de sjabloon Cocoa-app.

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
1. Stel de rechten in voor toegang tot het netwerk en de microfoon. Klik op de naam van de app in de eerste regel van het overzicht aan de linkerkant om naar de app-configuratie te gaan en kies vervolgens het tabblad Mogelijkheden.
    1. Schakel de instelling App-sandbox in voor de app.
    1. Schakel de selectievakjes voor Outgoing connections en toegang tot de microfoon (Microphone) in.
    ![Sandbox-instellingen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-sandbox.png)
1. De app moet ook het gebruik van de microfoon in het `Info.plist`-bestand declareren. Klik op het bestand in het overzicht en voeg de sleutel Privacy - Microphone Usage Description toe met een waarde als Microfoon is vereist voor spraakherkenning.
    ![Instellingen in Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-info-plist.png)
1. Sluit het Xcode-project. Later gebruikt u een ander exemplaar van het project als u de CocoaPods hebt ingesteld.

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Plaats een nieuw header-bestand met de naam `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` in de map `helloworld` in het project hallowereld en plak er de volgende code in:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Voeg het relatieve pad `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` naar de overbruggings-header toe aan de SWIFT-projectinstellingen voor het doel hallowereld in het *Objective-C Bridging Header*-veld ![Header properties](~/articles/cognitive-services/Speech-Service/media/sdk/qs-swift-macos-bridging-header.png)
1. Vervang de inhoud van het automatisch gegenereerde bestand `AppDelegate.swift` door:

   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/helloworld/AppDelegate.swift#code)]
1. Vervang in `AppDelegate.swift` de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.
1. Vervang de tekenreeks `YourServiceRegion` door de regio die aan uw abonnement is gekoppeld (bijvoorbeeld `westus` voor het gratis proefabonnement).

## <a name="install-the-sdk-as-a-cocoapod"></a>De SDK installeren als een CocoaPod

1. Installeer het CocoaPod-afhankelijkheidsbeheer zoals beschreven in de [installatie-instructies](https://guides.cocoapods.org/using/getting-started.html).
1. Ga naar de map van uw voorbeeld-app (`helloworld`). Plaats een tekstbestand met de naam `Podfile` en de volgende inhoud in die map:

   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift/macos/from-microphone/helloworld/Podfile)]
1. Ga in een terminal naar de map `helloworld` en voer opdracht `pod install` uit. Hiermee wordt een `helloworld.xcworkspace`-Xcode-werkruimte gegenereerd die de voorbeeld-app, de Speech SDK en een afhankelijkheid bevat. Deze werkruimte wordt in de volgende stappen gebruikt.

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. Open de werkruimte `helloworld.xcworkspace` in Xcode.
1. Maak de foutopsporingsuitvoer zichtbaar (**View** > **Debug Area** > **Activate Console**).
1. Bouw de voorbeeldcode en voer deze uit door **Product** > **Run** in het menu te selecteren of op de knop **Play** te klikken.
1. Nadat u op de knop Recognize in de app hebt geklikt en enkele woorden hebt gezegd, zou u de tekst die u hebt uitgesproken moeten zien in het onderste gedeelte van het app-venster.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Onze C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)

