---
title: 'Snelstart: setup van het SpraakSDK C++ (macOS) platform - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Gebruik deze handleiding om uw platform voor C++ in te stellen op macOS met de Spraakservice SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: d0f9bc44145f75d7c3b503fffe43042c650025f1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75468375"
---
In deze handleiding ziet u hoe u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) voor C++ installeren op macOS 10.13 en hoger.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Systeemvereisten

macOS 10.13 en hoger

## <a name="install-speech-sdk"></a>Speech SDK installeren

1. Kies een map waar de bestanden van de Speech SDK moeten worden uitgepakt en laat de `SPEECHSDK_ROOT`-omgevingsvariabele naar die map verwijzen. Met deze variabele kunt u in toekomstige opdrachten eenvoudig naar de map verwijzen. Als u de map `speechsdk` bijvoorbeeld wilt gebruiken in de basismap, gebruik dan een opdracht als de volgende:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Maak de map als deze nog niet bestaat.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Download en `.zip` haal het archief met het Speech SDK-framework:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Valideer de inhoud van de map op het hoogste niveau van het uitgepakte pakket:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   De directory vermelding moet de kennisgeving en licentiebestanden van `MicrosoftCognitiveServicesSpeech.framework` derden bevatten, evenals een directory.

U nu verder gaan naar [Volgende stappen](#next-steps) hieronder.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list.md)]
