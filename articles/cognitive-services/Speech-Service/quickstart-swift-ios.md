---
title: 'Quickstart: Spraak, snelle spraak service herkennen'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het herkennen van spraak in Swift op iOS met de Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: cdd09a860c3840823661e30c9f8ebcb900574531
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803211"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-using-the-speech-sdk"></a>Quickstart: Spraak in Swift op iOS herkennen met de Speech SDK

Quick starts zijn ook beschikbaar voor [spraak synthese](quickstart-text-to-speech-swift-ios.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In dit artikel leert u hoe u in SWIFT een iOS-app kunt maken met behulp van de SDK van Cognitive Services speech om spraak opnamen van een microfoon naar tekst te transcriberen.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, bekijkt u de lijst vereisten:

* Een [abonnements sleutel](get-started.md) voor de spraak service.
* Een macOS-computer met [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) of hoger en [CocoaPods](https://cocoapods.org/) geïnstalleerd.

## <a name="get-the-speech-sdk-for-ios"></a>De Speech-SDK voor iOS ophalen

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Houd er rekening mee dat deze zelf studie niet werkt met een versie van de SDK die ouder is dan 1.6.0.

De Cognitive Services Speech SDK voor iOS wordt gedistribueerd als een framework-bundel.
Het kan worden gebruikt in Xcode-projecten als een [CocoaPod](https://cocoapods.org/), of worden gedownload van https://aka.ms/csspeech/macosbinary en hand matig gekoppeld. Deze hand leiding maakt gebruik van een CocoaPod.

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Start Xcode en start een nieuw project starten door te klikken op **File** > **New** > **Project**.
Kies in het dialoogvenster voor het selecteren van sjablonen de sjabloon 'iOS Single View App'.

Maak in de dialoogvensters die volgen de volgende selecties:

1. Het dialoogvenster Project Options
    1. Voer een naam in voor de snelstart-app, bijvoorbeeld `helloworld`.
    1. Voer de juiste organisatie naam en een organisatie-id in als u al een Apple-ontwikkelaars account hebt. Voor testdoeleinden kunt u elke naam kiezen, zoals `testorg`. Om de app te kunnen ondertekenen, hebt u een geschikt inrichtingsprofiel nodig. Raadpleeg de [Apple-site voor ontwikkelaars](https://developer.apple.com/) voor meer informatie.
    1. Zorg ervoor dat SWIFT is gekozen als de taal voor het project.
    1. Schakel de selectie vakjes uit om Story boards te gebruiken en om een op documenten gebaseerde toepassing te maken. De eenvoudige gebruikers interface voor de voor beeld-app wordt programmatisch gemaakt.
    1. Schakel alle selectievakjes voor tests en essentiële gegevens uit.
1. Projectmap selecteren
    1. Kies een map waarin u het project wilt plaatsen. Hiermee maakt u een map @no__t 0 in de gekozen map die alle bestanden voor het Xcode-project bevat.
    1. Schakel het maken van een Git-opslagplaats uit voor dit voorbeeldproject.
1. De app moet ook het gebruik van de microfoon in het `Info.plist`-bestand declareren. Klik op het bestand in het overzicht en voeg de sleutel ' beschrijving privacy-microfoon gebruik ' toe, met een waarde als ' microfoon is vereist voor spraak herkenning '.
    ![Settings in info. plist @ no__t-1
1. Sluit het project Xcode. Later gebruikt u een ander exemplaar na het instellen van de CocoaPods.

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Plaats een nieuw header-bestand met de naam `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` in de map `helloworld` in het HelloWorld-project en plak de volgende code in deze.  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Voeg het relatieve pad `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` toe aan de bridging-header voor de SWIFT-project instellingen voor het doel HelloWorld in het veld *doel-C bridging header* ![Header eigenschappen @ no__t-3
1. Vervang de inhoud van het automatisch gegenereerde bestand `AppDelegate.swift` door:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Vervang de inhoud van het automatisch gegenereerde bestand `ViewController.swift` door:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. Vervang in `ViewController.swift` de teken reeks `YourSubscriptionKey` door de sleutel van uw abonnement.
1. Vervang de tekenreeks `YourServiceRegion` door de [regio](regions.md) die aan uw abonnement is gekoppeld (bijvoorbeeld `westus` voor het gratis proefabonnement).

## <a name="install-the-sdk-as-a-cocoapod"></a>De SDK installeren als een CocoaPod

1. Installeer de CocoaPod dependency manager zoals beschreven in de [installatie-instructies](https://guides.cocoapods.org/using/getting-started.html).
1. Ga naar de map van uw voor beeld-app (`helloworld`). Plaats een tekst bestand met de naam `Podfile` en de volgende inhoud in die map:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. Ga naar de map `helloworld` in een Terminal en voer de opdracht uit `pod install`. Hiermee wordt een Xcode-werk ruimte van @no__t 0 gegenereerd met zowel de voor beeld-app als de spraak-SDK als een afhankelijkheid. Deze werk ruimte wordt gebruikt in het volgende.

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. Open de werk ruimte `helloworld.xcworkspace` in Xcode.
1. Maak de foutopsporingsuitvoer zichtbaar (**View** > **Debug Area** > **Activate Console**).
1. Kies de iOS-Simulator of een iOS-apparaat dat is verbonden met uw ontwikkel computer als bestemming voor de app uit de lijst in het**doel** menu **product** > .
1. Bouw de voorbeeldcode en voer deze uit in de iOS-simulator door **Product** > **Run** in het menu te selecteren of op de knop **Play** te klikken.
1. Nadat u op de knop ' herkennen ' in de app hebt geklikt en enkele woorden hebt gedicteerd, ziet u de tekst die u hebt gesp roken op het onderste deel van het scherm.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Onze C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)
