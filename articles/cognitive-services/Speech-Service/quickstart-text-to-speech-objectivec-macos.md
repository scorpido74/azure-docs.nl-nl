---
title: 'Quickstart: Bewaak spraak, objectief-C-Speech-Service'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van spraak in doel-C op macOS met behulp van de Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: fc134de0c0a27f50aa8f3526cce59925dfa96982
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803969"
---
# <a name="quickstart-synthesize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Quickstart: Spraak in doel-C op macOS bewaakt met behulp van de Speech SDK

Quick starts zijn ook beschikbaar voor [spraak herkenning](quickstart-objective-c-macos.md).

In dit artikel leert u hoe u een macOS-app kunt maken in doel-C met behulp van de SDK van Cognitive Services speech om spraak van tekst te verwerken en af te spelen met de standaard audio-uitvoer.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, bekijkt u de lijst vereisten:

* Een [abonnementssleutel](get-started.md) voor Speech Service
* Een macOS-computer met [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) of hoger en MacOS 10,13 of hoger

## <a name="get-the-speech-sdk-for-macos"></a>De Speech SDK voor macOS ophalen

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Houd er rekening mee dat deze zelf studie niet werkt met een versie van de SDK die ouder is dan 1.7.0.

De Cognitive Services Speech SDK voor Mac wordt gedistribueerd als een framework-bundel.
Het kan worden gebruikt in Xcode-projecten als een [CocoaPod](https://cocoapods.org/), of worden gedownload van https://aka.ms/csspeech/macosbinary en hand matig gekoppeld. Deze hand leiding maakt gebruik van een CocoaPod.

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Start Xcode en start een nieuw project starten door te klikken op **File** > **New** > **Project**.
Kies in het dialoog venster sjabloon selectie de sjabloon ' cacao-app '.

Maak in de dialoogvensters die volgen de volgende selecties:

1. Het dialoogvenster Project Options
    1. Voer een naam in voor de snelstart-app, bijvoorbeeld `helloworld`.
    1. Voer de juiste organisatie naam en een organisatie-id in als u al een Apple-ontwikkelaars account hebt. Voor testdoeleinden kunt u elke naam kiezen, zoals `testorg`. Om de app te kunnen ondertekenen, hebt u een geschikt inrichtingsprofiel nodig. Raadpleeg de [Apple-site voor ontwikkelaars](https://developer.apple.com/) voor meer informatie.
    1. Zorg ervoor dat Objective-C als de taal voor het project is gekozen.
    1. Schakel de selectie vakjes uit om Story boards te gebruiken en om een op documenten gebaseerde toepassing te maken. De eenvoudige gebruikers interface voor de voor beeld-app wordt programmatisch gemaakt.
    1. Schakel alle selectievakjes voor tests en essentiële gegevens uit.
    ![Projectinstellingen](media/sdk/qs-objectivec-macos-project-settings.png)
1. Projectmap selecteren
    1. Kies een map waarin u het project wilt plaatsen. Hiermee maakt u een `helloworld`-map in uw basismap die alle bestanden voor het Xcode-project bevat.
    1. Schakel het maken van een Git-opslagplaats uit voor dit voorbeeldproject.
1. Stel de rechten in voor toegang tot het netwerk. Klik op de naam van de app in de eerste regel van het overzicht aan de linkerkant om naar de app-configuratie te gaan en kies vervolgens het tabblad mogelijkheden.
    1. Schakel de instelling app sandbox in voor de app.
    1. Schakel de selectie vakjes voor toegang tot uitgaande verbindingen in.
    ![Sandbox-instellingen @ no__t-1
1. Sluit het project Xcode. Later gebruikt u een ander exemplaar na het instellen van de CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>De SDK installeren als een CocoaPod

1. Installeer de CocoaPod dependency manager zoals beschreven in de [installatie-instructies](https://guides.cocoapods.org/using/getting-started.html).
1. Ga naar de map van uw voor beeld-app (`helloworld`). Plaats een tekst bestand met de naam `Podfile` en de volgende inhoud in die map:  
   [!code-ruby[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/objectivec-macos/helloworld/Podfile)]
1. Ga naar de map `helloworld` in een Terminal en voer de opdracht uit `pod install`. Hiermee wordt een Xcode-werk ruimte van @no__t 0 gegenereerd met zowel de voor beeld-app als de spraak-SDK als een afhankelijkheid. Deze werk ruimte wordt gebruikt in het volgende.

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Open de werk ruimte `helloworld.xcworkspace` in Xcode.
1. Vervang de inhoud van het automatisch gegenereerde bestand `AppDelegate.m` door:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Vervang de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.
1. Vervang de tekenreeks `YourServiceRegion` door de [regio](regions.md) die aan uw abonnement is gekoppeld (bijvoorbeeld `westus` voor het gratis proefabonnement).

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. Maak de foutopsporingsuitvoer zichtbaar (**View** > **Debug Area** > **Activate Console**).
1. Bouw de voorbeeld code en voer**deze uit door** in het menu **product** ->  uit te selecteren of door op de knop **afspelen** te klikken.
1. Nadat u tekst hebt ingevoerd en op de knop in de app hebt geklikt, moet u horen dat de geteste audio is afgespeeld.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Objective-C-voorbeelden op GitHub verkennen](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zie ook

- [Spraak lettertypen aanpassen](how-to-customize-voice-font.md)
- [Spraak voorbeelden vastleggen](record-custom-voice-samples.md)
