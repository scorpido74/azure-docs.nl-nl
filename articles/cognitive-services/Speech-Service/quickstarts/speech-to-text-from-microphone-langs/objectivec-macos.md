---
title: 'Snelstart: spraak herkennen van een microfoon, Objective-C - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het herkennen van spraak in Objective-C op macOS met behulp van de Speech SDK
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/23/2019
ms.author: chlandsi
ms.openlocfilehash: c2f0fbe66b26c6eca6e0c0b2530efacba9bae958
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75380589"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-by-using-the-speech-sdk"></a>Snelstart: spraak herkennen in Objective-C op macOS met behulp van de SpraakSDK

Quickstarts zijn ook beschikbaar voor [spraaksynthese.](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech-langs/objectivec-macos.md)

In dit artikel leert u hoe u een macOS-app maakt in Doelstelling-C met behulp van de Azure Cognitive Services Speech SDK om spraak die is opgenomen van een microfoon naar tekst te transcriberen.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, hebt u het:

* Een [abonnementssleutel](~/articles/cognitive-services/Speech-Service/get-started.md) voor de Spraakservice.
* Een macOS-machine met [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) of hoger en macOS 10.13 of hoger.

## <a name="get-the-speech-sdk-for-macos"></a>Download de SpraakSDK voor macOS

[!INCLUDE [License notice](~/includes/cognitive-services-speech-service-license-notice.md)]

De Cognitive Services Speech SDK voor Mac wordt gedistribueerd als een frameworkbundel. Het kan worden gebruikt in Xcode projecten als https://aka.ms/csspeech/macosbinary een [CocoaPod](https://cocoapods.org/) of gedownload van en handmatig gekoppeld. Dit artikel maakt gebruik van een CocoaPod.

## <a name="create-an-xcode-project"></a>Een Xcode-project maken

Start Xcode en start een nieuw project door **Bestand** > **Nieuw** > **project te**selecteren . Selecteer in het dialoogvenster Sjabloonselectie de sjabloon **Cacao-app.**

Maak in de volgende dialoogvensters de volgende selecties.

1. Ga als een in het dialoogvenster **Projectopties:**
    1. Voer een naam in voor de quickstart-app, bijvoorbeeld *helloworld*.
    1. Voer een geschikte organisatienaam en een organisatie-id in als u al een Apple-ontwikkelaarsaccount hebt. Gebruik voor testdoeleinden een naam als *testorg.* Om de app te kunnen ondertekenen, hebt u een geschikt inrichtingsprofiel nodig. Zie de [apple-ontwikkelaarssite](https://developer.apple.com/)voor meer informatie.
    1. Controleer of **Doelstelling-C** is geselecteerd als taal voor het project.
    1. Schakel de selectievakjes uit om storyboards te gebruiken en een toepassing op basis van documenten te maken. De eenvoudige gebruikersinterface voor de voorbeeld-app wordt programmatisch gemaakt.
    1. Schakel alle selectievakjes voor tests en kerngegevens uit.

    ![Projectinstellingen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-project-settings.png)

1. Selecteer een projectmap:
    1. Kies een map om het project in te plaatsen. Met deze stap wordt een helloworld-map gemaakt in uw thuismap die alle bestanden voor het Xcode-project bevat.
    1. Schakel het maken van een Git-opslagplaats uit voor dit voorbeeldproject.
1. Stel de rechten in voor toegang tot het netwerk en de microfoon. Selecteer de naam van de app in de eerste regel in het overzicht aan de linkerkant om naar de app-configuratie te gaan. Selecteer vervolgens het tabblad **Mogelijkheden.**
    1. Schakel de **sandbox-instelling app** in voor de app.
    1. Schakel de selectievakjes in voor **uitgaande verbindingen** en **microfoontoegang.**

    ![Sandbox-instellingen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-sandbox.png)

1. De app moet ook het gebruik `Info.plist` van de microfoon in het bestand aangeven. Selecteer het bestand in het overzicht en voeg de sleutel **Privacy - Beschrijvingvan het microfoongebruik** toe met een waarde zoals *microfoon is vereist voor spraakherkenning.*

    ![Instellingen in Info.plist](~/articles/cognitive-services/Speech-Service/media/sdk/qs-objectivec-macos-info-plist.png)

1. Sluit het Xcode-project. U gebruikt een ander exemplaar van het later nadat u de CocoaPods hebt ingesteld.

## <a name="install-the-sdk-as-a-cocoapod"></a>Installeer de SDK als CocoaPod

1. Installeer de CocoaPod afhankelijkheidsmanager zoals beschreven in de [installatie-instructies.](https://guides.cocoapods.org/using/getting-started.html)
1. Ga naar de directory van uw voorbeeld app, dat is helloworld. Plaats een tekstbestand met de naam *Podfile* en de volgende inhoud in die map:

   [!code-ruby[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/Podfile)]
1. Ga naar de helloworld directory in een `pod install`terminal en voer de opdracht uit. Met deze `helloworld.xcworkspace` opdracht genereert u een Xcode-werkruimte die zowel de voorbeeld-app als de Spraak-SDK als afhankelijkheid bevat. Deze werkruimte wordt gebruikt in de volgende stappen.

## <a name="add-the-sample-code"></a>De voorbeeldcode toevoegen

1. Open de `helloworld.xcworkspace` werkruimte in Xcode.
1. Vervang de inhoud van `AppDelegate.m` het automatisch gegenereerde bestand door de volgende code:

   [!code-objectivec[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec/macos/from-microphone/helloworld/helloworld/AppDelegate.m#code)]
1. Vervang de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.
1. Vervang de `YourServiceRegion` tekenreeks door het [gebied](~/articles/cognitive-services/Speech-Service/regions.md) dat aan uw abonnement is gekoppeld. Gebruik bijvoorbeeld `westus` voor het gratis proefabonnement.

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

1. Maak de foutopsporingsuitvoer zichtbaar door de console**Foutopsporingsgebied** >  **weergeven** > **te**selecteren .
1. Bouw en voer de voorbeeldcode uit door **Productrun** > in het menu**te** selecteren. Je ook **Afspelen**selecteren.
1. Nadat u de knop hebt geselecteerd en een paar woorden hebt gezegd, ziet u de tekst die u op het onderste deel van het scherm hebt gesproken. Wanneer u de app voor de eerste keer uitvoert, moet u worden gevraagd de app toegang te geven tot de microfoon van uw computer.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Objective-C-voorbeelden op GitHub verkennen](https://aka.ms/csspeech/samples)
