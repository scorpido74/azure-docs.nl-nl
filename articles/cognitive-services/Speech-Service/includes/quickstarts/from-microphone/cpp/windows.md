---
title: 'Snelstartgids: spraak van een microfoon herkennen C++ (Windows)-spraak service'
titleSuffix: Azure Cognitive Services
description: Informatie over het herkennen van gesproken tekst in C++ op Windows Desktop met behulp van de Speech SDK
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: wolfma
ms.openlocfilehash: 1a08a5413ae41ef8c17e817a01be9e9efcb7ff99
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503638"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Uw ontwikkel omgeving instellen](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Een leeg voorbeeld project maken](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open het bronbestand **helloworld.cpp**.

1. Vervang alle code door het volgende fragment:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

1. Vervang in hetzelfde bestand de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang de tekenreeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die gekoppeld is aan uw abonnement (bijvoorbeeld `westus` voor het gratis proefabonnement).

1. Kies in de menu balk de optie **bestand** > alles op te **slaan**.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

1. Selecteer in de menu balk de optie **build** > **Build** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **fout opsporing** > **fout opsporing starten** (of druk op **F5**) om de toepassing **HelloWorld** te starten.

1. Spreek een Engelse woordgroep of zin in. De toepassing stuurt uw spraak naar de spraak Services, die naar tekst transcribeert en stuurt deze terug naar de toepassing voor weer gave.

   ![Console-uitvoer na geslaagde herkenning](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]