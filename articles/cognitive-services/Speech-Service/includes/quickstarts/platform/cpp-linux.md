---
title: 'Quickstart: Speech SDK C++ (Linux) platform setup - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Gebruik deze handleiding om uw platform voor C++ op Linux in te stellen met de Spraakservice SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 4f211e4b90dcc8bffa2fbba6fa4783caf846f50c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78383948"
---
Deze handleiding laat zien hoe je de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) voor Linux installeert

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Systeemvereisten

Linux (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8)

## <a name="prerequisites"></a>Vereisten

Om deze snelle start te voltooien, heb je het:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* Ondersteunde Linux-platforms vereisen bepaalde geïnstalleerde`libssl` bibliotheken (voor ondersteuning `libasound2` van beveiligde sockets laag en voor geluidsondersteuning). Raadpleeg hieronder uw distributie voor de opdrachten die nodig zijn om de juiste versies van deze bibliotheken te installeren.

   * Op Ubuntu:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * Op Debian 9:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

   * Op RHEL/CentOS 8:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> Volg op RHEL/CentOS 8 de instructies voor [het configureren van OpenSSL voor Linux.](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)

## <a name="install-speech-sdk"></a>Speech SDK installeren

De Speech SDK voor Linux kan worden gebruikt om zowel 32-bits als 64-bits toepassingen te compileren. De vereiste bibliotheken en headerbestanden kunnen als een TAR-bestand worden gedownload vanuit https://aka.ms/csspeech/linuxbinary.

Download en installeer de SDK als volgt:

1. Kies een map waar de bestanden van de Speech SDK moeten worden uitgepakt en laat de `SPEECHSDK_ROOT`-omgevingsvariabele naar die map verwijzen. Met deze variabele kunt u in toekomstige opdrachten eenvoudig naar de map verwijzen. Als u de map `speechsdk` bijvoorbeeld wilt gebruiken in de basismap, gebruik dan een opdracht als de volgende:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Maak de map als deze nog niet bestaat.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Download en extraheer het `.tar.gz`-archief met de binaire bestanden voor de Speech SDK:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Valideer de inhoud van de map op het hoogste niveau van het uitgepakte pakket:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   De mapvermelding moet de kennisgeving van derden en licentiebestanden bevatten, evenals een `include`-map met headerbestanden (`.h`) en een `lib`-map met bibliotheken.

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]

U nu verder gaan naar [Volgende stappen](#next-steps) hieronder.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list.md)]
