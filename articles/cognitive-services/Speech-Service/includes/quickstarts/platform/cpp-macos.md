---
title: 'Quick Start: Speech C++ -platform Setup (macOS)-spraak service'
titleSuffix: Azure Cognitive Services
description: Gebruik deze hand leiding om uw platform in te C++ stellen voor macOS met de speech Services SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 2c6a186eef631372f08b876083dacede31cf1077
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502546"
---
In deze hand leiding wordt uitgelegd hoe u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) installeert voor C++ macOS 10,13 en hoger.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Systeemvereisten

macOS 10,13 en hoger

## <a name="install-speech-sdk"></a>Speech SDK installeren

1. Kies een map waar de bestanden van de Speech SDK moeten worden uitgepakt en laat de `SPEECHSDK_ROOT`-omgevingsvariabele naar die map verwijzen. Met deze variabele kunt u in toekomstige opdrachten eenvoudig naar de map verwijzen. Als u de map `speechsdk` bijvoorbeeld wilt gebruiken in de basismap, gebruik dan een opdracht als de volgende:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Maak de map als deze nog niet bestaat.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Down load en pak het `.zip` archief met het Speech SDK Framework:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Valideer de inhoud van de map op het hoogste niveau van het uitgepakte pakket:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   De mapweergave moet de kennisgevings-en licentie bestanden van derden bevatten, evenals een `MicrosoftCognitiveServicesSpeech.framework` Directory.

U kunt nu door gaan naar de [volgende stappen](#next-steps) .

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list.md)]
