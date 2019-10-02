---
title: 'Quickstart: Speech herkennen, C++ (macOS)-Speech Service'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het herkennen van C++ spraak in macOS met behulp van de Speech SDK
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 12abb7fb6d5e93e226ae539f8161566e3c30bd65
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803331"
---
# <a name="quickstart-recognize-speech-in-c-on-macos-by-using-the-speech-sdk"></a>Quickstart: Spraak herkennen in C++ macOS met behulp van de Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In dit artikel maakt u een C++ console toepassing voor macOS 10,13 en hoger. U gebruikt de Cognitive Services [Speech SDK](speech-sdk.md) om spraak naar tekst in realtime te transcriberen van de microfoon van uw Mac. De toepassing is gebouwd met de [Speech SDK voor macOS](https://aka.ms/csspeech/macosbinary) en de standaard C++ compiler van uw Mac (bijvoorbeeld `g++`).

## <a name="prerequisites"></a>Vereisten

U hebt een abonnement op spraak services nodig om deze Quick Start te volt ooien. U kunt er gratis een krijgen. Zie [de spraak services gratis uitproberen](get-started.md) voor meer informatie.

## <a name="install-speech-sdk"></a>Speech SDK installeren

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

De Speech SDK voor macOS kan worden gedownload als een gezipte Framework-bundel van https://aka.ms/csspeech/macosbinary.

Download en installeer de SDK als volgt:

1. Kies een map waar de bestanden van de Speech SDK moeten worden uitgepakt en laat de `SPEECHSDK_ROOT`-omgevingsvariabele naar die map verwijzen. Met deze variabele kunt u in toekomstige opdrachten eenvoudig naar de map verwijzen. Als u de map `speechsdk` bijvoorbeeld wilt gebruiken in de basismap, gebruik dan een opdracht als de volgende:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Maak de map als deze nog niet bestaat.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Down load en Extraheer het `.zip`-archief dat het Speech SDK-Framework bevat:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Valideer de inhoud van de map op het hoogste niveau van het uitgepakte pakket:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   De mapweergave moet de kennisgevings-en licentie bestanden van derden bevatten, evenals een map `MicrosoftCognitiveServicesSpeech.framework`.

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Maak een C++ bronbestand met de naam `helloworld.cpp` en plak er de volgende code in.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-macos/helloworld.cpp#code)]

1. Vervang in dit nieuwe bestand de teken reeks `YourSubscriptionKey` door uw abonnements sleutel voor spraak Services.

1. Vervang de tekenreeks `YourServiceRegion` door de [regio](regions.md) die aan uw abonnement is gekoppeld (bijvoorbeeld `westus` voor het gratis proefabonnement).

## <a name="build-the-app"></a>De app bouwen

> [!NOTE]
> Zorg ervoor dat u de onderstaande opdrachten als _één opdrachtregel_ invoert. De eenvoudigste manier om dat te doen is om de opdracht te kopiëren met behulp van de knop **Kopiëren** naast elke opdracht en deze vervolgens bij de shell-prompt te plakken.

* Voer de volgende opdracht uit om de toepassing te bouwen.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>De app uitvoeren

1. Configureer het bibliotheekpad van het laadprogramma om te verwijzen naar de bibliotheek van de Speech SDK.

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. Voer de toepassing uit.

   ```sh
   ./helloworld
   ```

1. In het consolevenster wordt een prompt weergegeven waarin u wordt gevraagd om iets te zeggen. Spreek een Engelse woordgroep of zin in. Uw spraak wordt verzonden naar de spraak Services en getranscribeerd naar tekst, die in hetzelfde venster wordt weer gegeven.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [C++-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Zie ook

- [Akoestische modellen aanpassen](how-to-customize-acoustic-models.md)
- [Taalmodellen aanpassen](how-to-customize-language-model.md)
