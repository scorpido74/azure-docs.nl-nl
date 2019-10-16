---
title: 'Snelstartgids: spraak, Swift-Speech-Service herkennen'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het herkennen van spraak in Swift op iOS met behulp van de Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 0a62e670587f271eb9a1beba034886c5f95976a7
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327748"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-by-using-the-speech-sdk"></a>Snelstartgids: spraak in Swift op iOS herkennen met de Speech SDK

Quick starts zijn ook beschikbaar voor [spraak synthese](quickstart-text-to-speech-swift-ios.md).

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In dit artikel leert u hoe u in SWIFT een iOS-app kunt maken met behulp van de Azure Cognitive Services Speech SDK om spraak van een microfoon naar tekst te transcriberen.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, hebt u het volgende nodig:

* Een [abonnements sleutel](get-started.md) voor de spraak service.
* Een macOS-computer met [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) of hoger en [CocoaPods](https://cocoapods.org/) geïnstalleerd.

## <a name="get-the-speech-sdk-for-ios"></a>De Speech-SDK voor iOS ophalen

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Deze zelf studie werkt niet met een versie van de SDK die ouder is dan 1.6.0.

De Cognitive Services Speech SDK voor iOS wordt gedistribueerd als een framework-bundel. Het kan worden gebruikt in Xcode-projecten als [CocoaPod](https://cocoapods.org/) of worden gedownload van https://aka.ms/csspeech/macosbinary en hand matig worden gekoppeld. In dit artikel wordt gebruikgemaakt van een CocoaPod.

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Start Xcode en start een nieuw project door **bestand** > **Nieuw** > -**project**te selecteren.
Selecteer in het dialoog venster sjabloon selectie de sjabloon **voor de IOS-app met één weer gave** .

In de volgende dialoog vensters volgt u de onderstaande opties.

1. In het dialoog venster **project opties** :
    1. Voer een naam in voor de Quick Start-app, bijvoorbeeld *HelloWorld*.
    1. Voer de juiste organisatie naam en organisatie-id in als u al een Apple-ontwikkelaars account hebt. Gebruik voor test doeleinden een naam zoals *testorg*. Om de app te kunnen ondertekenen, hebt u een geschikt inrichtingsprofiel nodig. Zie de [Apple Developer-site](https://developer.apple.com/)voor meer informatie.
    1. Zorg ervoor dat **Swift** is gekozen als de taal voor het project.
    1. Schakel de selectie vakjes uit om Story boards te gebruiken en om een op documenten gebaseerde toepassing te maken. De eenvoudige gebruikers interface voor de voor beeld-app wordt programmatisch gemaakt.
    1. Schakel alle selectie vakjes voor testen en basis gegevens uit.
1. Selecteer een projectmap:
    1. Kies een map waarin u het project wilt plaatsen. Met deze stap maakt u een map HelloWorld in de gekozen map met alle bestanden voor het Xcode-project.
    1. Schakel het maken van een Git-opslagplaats uit voor dit voorbeeldproject.
1. De app moet ook het gebruik van de microfoon in het `Info.plist`-bestand declareren. Selecteer het bestand in het overzicht en voeg de sleutel **gebruiks beschrijving privacy-microfoon** toe met een waarde als *microfoon is vereist voor spraak herkenning*.

    ![Instellingen in info. plist](media/sdk/qs-swift-ios-info-plist.png)

1. Sluit het project Xcode. U kunt later een ander exemplaar van het item gebruiken nadat u de CocoaPods hebt ingesteld.

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Plaats een nieuw header-bestand met de naam `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` in de map HelloWorld in het project HelloWorld. Plak de volgende code in het bestand:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]

1. Voeg het relatieve pad `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` toe aan de bridging-header naar de SWIFT-project instellingen voor het doel HelloWorld in het veld **doel-C bridging** .

   ![Eigenschappen van koptekst](media/sdk/qs-swift-ios-bridging-header.png)

1. Vervang de inhoud van het automatisch gegenereerde bestand `AppDelegate.swift` door de volgende code:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Vervang de inhoud van het automatisch gegenereerde bestand `ViewController.swift` door de volgende code:

   [!code-swift[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. Vervang in `ViewController.swift` de teken reeks `YourSubscriptionKey` door de sleutel van uw abonnement.
1. Vervang de teken reeks `YourServiceRegion` door de [regio](regions.md) die aan uw abonnement is gekoppeld. Gebruik bijvoorbeeld `westus` voor het gratis proef abonnement.

## <a name="install-the-sdk-as-a-cocoapod"></a>De SDK installeren als een CocoaPod

1. Installeer de CocoaPod dependency manager zoals beschreven in de [installatie-instructies](https://guides.cocoapods.org/using/getting-started.html).
1. Ga naar de map van uw voor beeld-app. Dit is HelloWorld. Plaats een tekst bestand met de naam *Podfile* en de volgende inhoud in die map:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. Ga naar de map HelloWorld in een Terminal en voer de opdracht uit `pod install`. Met deze opdracht wordt een Xcode-werk ruimte van @no__t 0 gegenereerd die zowel de voor beeld-app als de spraak-SDK als een afhankelijkheid bevat. Deze werk ruimte wordt in de volgende stappen gebruikt.

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. Open de werk ruimte `helloworld.xcworkspace` in Xcode.
1. Maak de uitvoer van de fout opsporing zichtbaar door **weer gave** > **debuggebied** > **console activeren**te selecteren.
1. Kies de iOS-Simulator of een iOS-apparaat dat is verbonden met uw ontwikkel computer als bestemming voor de app uit de lijst in het**doel** menu **product** > .
1. Bouw en voer de voorbeeld code in de iOS-Simulator uit door **Product** > **uitvoeren** te selecteren in het menu. U kunt ook de knop **afspelen** selecteren.
1. Nadat u de knop **herkennen** in de app hebt geselecteerd en enkele woorden hebt gedicteerd, ziet u de tekst die u in het onderste gedeelte van het scherm hebt gesp roken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Onze C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)
