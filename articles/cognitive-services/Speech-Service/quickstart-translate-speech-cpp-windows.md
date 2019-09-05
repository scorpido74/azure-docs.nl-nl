---
title: 'Quickstart: Vertaal spraak, C++ (Windows)-Speech Service'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start maakt u een C++ toepassing voor het vastleggen van gebruikers spraak, het vertalen ervan naar een andere taal en het uitvoeren van de tekst naar de opdracht regel. Deze handleiding is bedoeld voor Windows-gebruikers.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/24/2019
ms.author: erhopf
ms.openlocfilehash: 6ba4e44efc7ff24aa48f9f16840b2248423f7241
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382686"
---
# <a name="quickstart-translate-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Quickstart: Spraak in C++ Windows vertalen met behulp van de Speech SDK

Quick starts zijn ook beschikbaar voor [spraak herkenning](quickstart-cpp-windows.md) en [spraak synthese](quickstart-text-to-speech-cpp-windows.md).

In deze Quick Start maakt u een C++ toepassing die de gebruikers spraak van de microfoon van uw computer vastlegt, de spraak vertaalt en de omgezette tekst in realtime overschakelt naar de opdracht regel. Deze toepassing is gebouwd met het [Speech SDK NuGet-pakket](https://aka.ms/csspeech/nuget) en micro soft Visual Studio 2019 (alle edities).

Zie [Taalondersteuning](language-support.md) voor een volledige lijst met talen die voor spraakomzetting beschikbaar zijn.

## <a name="prerequisites"></a>Vereisten

U hebt een abonnement op spraak services nodig om deze Quick Start te volt ooien. U kunt er gratis een krijgen. Zie [de spraak services gratis uitproberen](get-started.md) voor meer informatie.

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open het bronbestand **helloworld.cpp**.

1. Vervang alle code door het volgende fragment:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/cpp-windows/helloworld/helloworld.cpp?range=2-#code)]

1. Vervang in hetzelfde bestand de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang de tekenreeks `YourServiceRegion` door de [regio](regions.md) die aan uw abonnement is gekoppeld (bijvoorbeeld `westus` voor het gratis proefabonnement).

1. Kies in de menu balk de optie **bestand** > **Opslaan**.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

1. Selecteer > in de menu balk build**Build Solution** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **debug** > **Start Debugging** (of druk op **F5**) om de toepassing **HelloWorld** te starten.

1. Spreek een Engelse woordgroep of zin in. De toepassing stuurt uw spraak naar de spraak Services, die worden vertaald naar tekst (in dit geval naar Frans en Duits). De spraak Services verzenden de tekst vervolgens terug naar de toepassing om weer te geven.

   ![Console-uitvoer na geslaagde spraak omzetting](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>Volgende stappen

Aanvullende voor beelden, zoals het lezen van spraak van een audio bestand of het omzetten van tekst in gesynthesizerde spraak, zijn beschikbaar op GitHub.

> [!div class="nextstepaction"]
> [C++-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zie ook

- [Een model trainen voor Custom Speech](how-to-custom-speech-train-model.md)
