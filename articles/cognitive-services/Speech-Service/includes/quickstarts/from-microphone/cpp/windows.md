---
title: 'Snelstart: spraak herkennen van een microfoon, C++ (Windows) - Spraakservice'
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
ms.openlocfilehash: 416c2893ebf07cd638f3f1c06c709db2586245d1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78924955"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * [Een Azure-spraakbron maken](../../../../get-started.md)
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=windows)
> * Zorg ervoor dat u toegang hebt tot een microfoon voor audio-opname

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open het bronbestand **helloworld.cpp**.

1. Vervang alle code door het volgende fragment:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

1. Vervang in hetzelfde bestand de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang de `YourServiceRegion` tekenreeks door de **regio-id** van [regio](https://aka.ms/speech/sdkregion) die is gekoppeld aan uw abonnement (bijvoorbeeld `westus` voor het gratis proefabonnement).

1. Kies Alles**opslaan** **op** > de menubalk .

> [!NOTE]
> De Spraak-SDK wordt standaard herkend door het gebruik van en-ons voor de taal, zie [Brontaal opgeven voor spraak naar tekst](../../../../how-to-specify-source-language.md) voor informatie over het kiezen van de brontaal.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

1. Selecteer op de menubalk **Build** > **Build Solution** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **Foutopsporing debuggen** > **(of** druk op **F5)** om de **helloworld-toepassing** te starten.

1. Spreek een Engelse woordgroep of zin in. De toepassing verzendt uw toespraak naar de spraakservice, die overschrijft naar tekst en deze terugstuurt naar de toepassing voor weergave.

   ![Console-uitvoer na succesvolle herkenning](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]