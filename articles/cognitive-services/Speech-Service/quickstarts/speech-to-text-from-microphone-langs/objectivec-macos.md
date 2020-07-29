---
title: 'Quickstart: Spraak herkennen, Objective-C - Speech Services (macOS)'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een app voor spraakherkenning in Objective-C voor een macOS-computer met de Cognitive Services Speech SDK.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: chlandsi
ms.openlocfilehash: b73925165552fe428be7df465b33701eeed157cc
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524230"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>Quickstart: Gesproken tekst herkennen in Objective-C in macOS met behulp van de Speech SDK

Snelstarts zijn ook beschikbaar voor [spraaksynthese](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-macos.md).

In dit artikel leert u hoe u een macOS-app in Objective-C maakt met behulp van de Azure Cognitive Services Speech SDK om met een microfoon opgenomen spraak om te zetten in tekst.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, hebt u het volgende nodig:

* Een [abonnementssleutel](~/articles/cognitive-services/Speech-Service/get-started.md) voor de Speech-service.
* Een macOS-machine waarop [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) of hoger en macOS 10.13 of hoger is geïnstalleerd.

## <a name="get-the-speech-sdk-for-macos"></a>De Speech SDK voor macOS ophalen

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

De Cognitive Services Speech SDK voor Mac wordt gedistribueerd als een frameworkbundel. De SDK kan in Xcode-projecten worden gebruikt als een [CocoaPod](https://cocoapods.org/) of worden gedownload van https://aka.ms/csspeech/macosbinary en handmatig worden gekoppeld. In dit artikel wordt gebruikgemaakt van een CocoaPod.

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Start Xcode en start een nieuw project door **File** > **New** > **Project** te selecteren. Selecteer in het dialoogvenster voor het selecteren van sjablonen de sjabloon **Cocoa-app**.

Maak in de dialoogvensters die volgen de volgende selecties.

1. In het dialoogvenster **Project Options**:
    1. Voer een naam in voor de snelstart-app, bijvoorbeeld *hallowereld*.
    1. Voer een relevante organisatienaam en organisatie-id in, als u al een Apple-ontwikkelaarsaccount hebt. Gebruik voor testdoeleinden een naam als *testorg*. Om de app te kunnen ondertekenen, hebt u een geschikt inrichtingsprofiel nodig. Zie de [site voor Apple-ontwikkelaars](https://developer.apple.com/) voor meer informatie.
    1. Zorg ervoor dat **Objective-C** als de taal voor het project is gekozen.
    1. Schakel de selectievakjes uit om storyboards te gebruiken en een op documenten gebaseerde toepassing te maken. De eenvoudige gebruikersinterface voor de voorbeeld-app wordt programmatisch gemaakt.
    1. Schakel alle selectievakjes voor testen en basisgegevens uit.

    ![Projectinstellingen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)

1. Selecteer een projectmap:
    1. Kies een map waarin u het project wilt plaatsen. Hiermee maakt u een hallowereld-map in de basismap met alle bestanden voor het Xcode-project.
    1. Schakel het maken van een Git-opslagplaats uit voor dit voorbeeldproject.
1. Stel de rechten in voor toegang tot het netwerk en de microfoon. Selecteer de naam van de app in de eerste regel van het overzicht aan de linkerkant om naar de app-configuratie te gaan. Selecteer vervolgens het tabblad **Mogelijkheden**.
    1. Schakel de instelling **App-sandbox** in voor de app.
    1. Schakel de selectievakjes voor **Outgoing Connections** en toegang tot de microfoon (**Microphone**) in.

    ![Sandbox-instellingen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox.png)

1. De app moet ook het gebruik van de microfoon in het `Info.plist`-bestand declareren. Selecteer het bestand in het overzicht en voeg de sleutel **Privacy - Microphone Usage Description** toe met een waarde als *Microfoon is vereist voor spraakherkenning*.

    ![Instellingen in Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-info-plist.png)

1. Sluit het Xcode-project. Later gebruikt u een ander exemplaar van het project als u de CocoaPods hebt ingesteld.

## <a name="install-the-sdk-as-a-cocoapod"></a>De SDK installeren als een CocoaPod

1. Installeer het CocoaPod-afhankelijkheidsbeheer zoals beschreven in de [installatie-instructies](https://guides.cocoapods.org/using/getting-started.html).
1. Ga naar de map van uw voorbeeld-app, hallowereld. Plaats een tekstbestand met de naam *Podfile* en de volgende inhoud in die map:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/Podfile)]
1. Ga in een terminal naar de map hallowereld en voer opdracht `pod install` uit. Met deze opdracht wordt een `helloworld.xcworkspace`-Xcode-werkruimte gegenereerd die de voorbeeld-app, de Speech SDK en een afhankelijkheid bevat. Deze werkruimte wordt in de volgende stappen gebruikt.

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Open de werkruimte `helloworld.xcworkspace` in Xcode.
1. Vervang de inhoud van het automatisch gegenereerde `AppDelegate.m`-bestand door de volgende code:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/helloworld/AppDelegate.m#code)]
1. Vervang de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.
1. Vervang de tekenreeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld. Gebruik bijvoorbeeld `westus` voor het gratis proefabonnement.

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. Maak de foutopsporingsuitvoer zichtbaar door **View** > **Debug Area** > **Activate Console** te selecteren.
1. Bouw de voorbeeldcode en voer deze uit door **Product** > **Run** in het menu te selecteren. U kunt ook **Play** selecteren.
1. Nadat u de knop hebt geselecteerd en enkele woorden hebt gesproken, zou u de uitgesproken tekst in het onderste gedeelte van het scherm moeten kunnen zien. Wanneer u de app voor de eerste keer uitvoert, wordt u gevraagd om de app toegang te geven tot de microfoon van uw computer.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Objective-C-voorbeelden op GitHub verkennen](https://aka.ms/csspeech/samples)
