---
title: 'Snelstart: spraak synthetiseren, C++ (Windows) - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het synthetiseren van spraak in C++ op Windows Desktop met de SpraakSDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: ab2193a1ea34b176e5f97806f0099dfc86d75965
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925732"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u:

> [!div class="checklist"]
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=windows)

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open het bronbestand **helloworld.cpp**.

1. Vervang alle code door het volgende fragment:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/text-to-speech/helloworld/helloworld.cpp#code)]

1. Vervang in hetzelfde bestand de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang de tekenreeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld (bijvoorbeeld `westus` voor het gratis proefabonnement).

1. Kies Alles**opslaan** **op** > de menubalk .

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

1. Selecteer op de menubalk **Build** > **Build Solution** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **Foutopsporing debuggen** > **(of** druk op **F5)** om de **helloworld-toepassing** te starten.

1. Typ een Engelse zin of zin. De toepassing verzendt uw tekst naar de spraakservice, die gesynthetiseerde spraak naar de toepassing verzendt om op uw luidspreker af te spelen.

   ![Console-uitvoer na succesvolle spraaksynthese](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste spraakvoorbeelden opnemen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
