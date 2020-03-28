---
title: 'Snelstart: Spraak synthetiseren, Objective-C - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het synthetiseren van spraak in Objective-C op macOS met de SpraakSDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: 37eed03ed839411f1acf5d963d4118a3c6d2c379
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975921"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Snelstart: spraak synthetiseren in Objective-C op macOS met de SpraakSDK

In dit artikel leert u hoe u een macOS-app maakt in Doelstelling-C met behulp van de Cognitive Services Speech SDK om spraak uit tekst te synthetiseren en af te spelen met de standaardaudio-uitvoer.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, bekijkt u de lijst vereisten:

* Een [abonnementssleutel](~/articles/cognitive-services/Speech-Service/get-started.md) voor de Spraakservice
* Een macOS-machine met [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) of hoger en macOS 10.13 of hoger

## <a name="get-the-speech-sdk-for-macos"></a>Download de SpraakSDK voor macOS

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Houd er rekening mee dat deze zelfstudie niet eerder dan 1.7.0 werkt met de versie van de SDK.

De Cognitive Services Speech SDK voor Mac wordt gedistribueerd als een frameworkbundel.
Het kan worden gebruikt in Xcode projecten als https://aka.ms/csspeech/macosbinary een [CocoaPod,](https://cocoapods.org/)of gedownload van en handmatig gekoppeld. Deze gids maakt gebruik van een CocoaPod.

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Start Xcode en start een nieuw project door op**New** > Nieuw**project** **bestand** > te klikken .
Kies in het dialoogvenster sjabloonselectie de sjabloon 'Cacao-app'.

Maak in de dialoogvensters die volgen de volgende selecties:

1. Het dialoogvenster Project Options
    1. Voer een naam in voor de snelstart-app, bijvoorbeeld `helloworld`.
    1. Voer een geschikte organisatienaam en een organisatie-id in als u al een Apple-ontwikkelaarsaccount hebt. Voor testdoeleinden kunt u elke naam kiezen, zoals `testorg`. Om de app te kunnen ondertekenen, hebt u een geschikt inrichtingsprofiel nodig. Raadpleeg de [Apple-site voor ontwikkelaars](https://developer.apple.com/) voor meer informatie.
    1. Zorg ervoor dat Objective-C als de taal voor het project is gekozen.
    1. Schakel de selectievakjes uit om storyboards te gebruiken en een toepassing op basis van documenten te maken. De eenvoudige gebruikersinterface voor de voorbeeld-app wordt programmatisch gemaakt.
    1. Schakel alle selectievakjes voor tests en essentiële gegevens uit.
    ![Projectinstellingen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)
1. Projectmap selecteren
    1. Kies een map om het project in te plaatsen. Hiermee maakt u een `helloworld`-map in uw basismap die alle bestanden voor het Xcode-project bevat.
    1. Schakel het maken van een Git-opslagplaats uit voor dit voorbeeldproject.
1. Stel de rechten voor netwerktoegang in. Klik op de naam van de app in de eerste regel in het overzicht aan de linkerkant om naar de app-configuratie te gaan en kies het tabblad 'Mogelijkheden'.
    1. Schakel de instelling 'Sandbox'voor de app in.
    1. Schakel de selectievakjes in voor toegang tot uitgaande verbindingen.
    ![Sandbox-instellingen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox-tts.png)
1. Sluit het Xcode-project. U gebruikt er later een ander exemplaar van na het instellen van de CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installeer de SDK als CocoaPod

1. Installeer de CocoaPod afhankelijkheidsmanager zoals beschreven in de [installatie-instructies.](https://guides.cocoapods.org/using/getting-started.html)
1. Navigeer naar de map van`helloworld`uw voorbeeld-app ( ). Plaats een tekstbestand `Podfile` met de naam en de volgende inhoud in die map:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/Podfile)]
1. Navigeer naar `helloworld` de map in een `pod install`terminal en voer de opdracht uit. Hierdoor wordt `helloworld.xcworkspace` een Xcode-werkruimte gegenereerd die zowel de voorbeeld-app als de Spraak-SDK als afhankelijkheid bevat. Deze werkruimte wordt als volgt gebruikt.

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Open `helloworld.xcworkspace` de werkruimte in Xcode.
1. Vervang de inhoud van het automatisch gegenereerde bestand `AppDelegate.m` door:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/text-to-speech/helloworld/helloworld/AppDelegate.m#code)]
1. Vervang de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.
1. Vervang de tekenreeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld (bijvoorbeeld `westus` voor het gratis proefabonnement).

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. De foutopsporingsuitvoer zichtbaar maken **(Console** > **foutopsporingsgebied** > **weergeven).**
1. Bouw en voer de voorbeeldcode uit door **Product** -> **run** te selecteren in het menu of op de knop **Afspelen** te klikken.
1. Nadat u tekst hebt ingevoerd en op de knop in de app hebt geklikt, hoort u de gesynthetiseerde audio die wordt afgespeeld.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Objective-C-voorbeelden op GitHub verkennen](https://aka.ms/csspeech/samples)

