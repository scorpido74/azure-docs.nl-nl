---
title: 'Snelstartgids: spraak van een microfoon herkennen C++ (Windows)-spraak service'
titleSuffix: Azure Cognitive Services
description: Informatie over het herkennen van gesproken tekst in C++ op Windows Desktop met behulp van de Speech SDK
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/17/2019
ms.author: wolfma
ms.openlocfilehash: e1385dd17e686370508576ec0cb713820f27f6b2
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75927769"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Uw ontwikkel omgeving instellen](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Een leeg voorbeeld project maken](../../../../quickstarts/create-project.md?tabs=windows)
> * Zorg ervoor dat u toegang tot een microfoon hebt voor het vastleggen van audio

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open het bronbestand **helloworld.cpp**.

1. Vervang alle code door het volgende fragment:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

1. Vervang in hetzelfde bestand de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang de tekenreeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die gekoppeld is aan uw abonnement (bijvoorbeeld `westus` voor het gratis proefabonnement).

1. Kies in de menu balk de optie **bestand** > alles op te **slaan**.

> [!NOTE]
> De spraak-SDK wordt standaard herkend door en-US voor de taal. Zie de [bron taal voor spraak opgeven](../../../../how-to-specify-source-language.md) voor de tekst voor informatie over het kiezen van de bron taal.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

1. Selecteer in de menu balk de optie **build** > **Build** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **fout opsporing** > **fout opsporing starten** (of druk op **F5**) om de toepassing **HelloWorld** te starten.

1. Spreek een Engelse woordgroep of zin in. De toepassing stuurt uw spraak naar de speech-service, die naar tekst overdraagt en verzendt deze terug naar de toepassing voor weer gave.

   ![Console-uitvoer na geslaagde herkenning](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]