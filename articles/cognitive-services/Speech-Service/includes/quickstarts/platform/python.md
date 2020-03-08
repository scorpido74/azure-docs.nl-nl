---
title: 'Snelstartgids: Speech SDK voor python platform Setup-Speech Service'
titleSuffix: Azure Cognitive Services
description: Gebruik deze hand leiding om uw platform in te stellen voor het gebruik van python met de Speech Service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/09/2019
ms.author: erhopf
ms.openlocfilehash: 458a6940ce214ef1931a2cc9ee95f2cb5ca16779
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925227"
---
In deze hand leiding wordt beschreven hoe u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) voor python installeert. Als u alleen de naam van het pakket wilt gebruiken om aan de slag te gaan, voert u `pip install azure-cognitiveservices-speech`uit.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

- Het Python Speech-SDK-pakket is beschikbaar voor deze besturingssystemen:
  - Windows: x64 en x86
  - Mac: macOS X versie 10,12 of hoger
  - Linux: Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8 op x64

## <a name="prerequisites"></a>Vereisten

- Voor ondersteunde Linux-platformen moeten bepaalde bibliotheken zijn geïnstalleerd (`libssl` voor de ondersteuning van Secure Sockets Layer en `libasound2` voor geluids ondersteuning). Raadpleeg de onderstaande distributie voor de opdrachten die nodig zijn om de juiste versies van deze bibliotheken te installeren.

  - Voer op Ubuntu de volgende opdrachten uit om de vereiste pakketten te installeren:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - Voer op Debian 9 de volgende opdrachten uit om de vereiste pakketten te installeren:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

  - Voer op RHEL/CentOS 8 de volgende opdrachten uit om de vereiste pakketten te installeren:

        ```sh
        sudo yum update
        sudo yum install alsa-lib openssl python3
        ```

> [!NOTE]
> Volg in RHEL/CentOS 8 de instructies voor het [configureren van openssl voor Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- In Windows hebt u [micro soft Visual C++ Redistributable voor Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) voor uw platform nodig. Houd er rekening mee dat als u dit voor de eerste keer installeert, Windows opnieuw moet worden opgestart voordat u doorgaat met deze hand leiding.
- En ten slotte hebt u [Python 3,5 tot 3,8](https://www.python.org/downloads/)nodig. Als u de installatie wilt controleren, opent u een opdracht prompt en typt u de opdracht `python --version` en controleert u het resultaat. Als de toepassing correct is geïnstalleerd, ontvangt u een antwoord "python 3.5.1" of soortgelijk.

## <a name="install-the-speech-sdk-from-pypi"></a>De Speech SDK installeren vanuit PyPI

Als u uw eigen omgeving gebruikt of hulpprogram ma's bouwt, voert u de volgende opdracht uit om de Speech SDK te installeren vanuit [PyPI](https://pypi.org/). Voor gebruikers van Visual Studio code gaat u naar de volgende Subsectie voor de begeleide installatie.

```sh
pip install azure-cognitiveservices-speech
```

Als u zich in macOS bevindt, moet u mogelijk de volgende opdracht uitvoeren om de bovenstaande `pip`-opdracht te laten werken:

```sh
python3 -m pip install --upgrade pip
```

Wanneer u `pip` hebt gebruikt om `azure-cognitiveservices-speech`te installeren, kunt u de Speech SDK gebruiken door de naam ruimte in uw python-projecten te importeren.

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>De Speech SDK installeren met Visual Studio code

1. Down load en installeer de meest recente ondersteunde versie van [python](https://www.python.org/downloads/) voor uw platform 3,5 tot 3,8.
   - Windows-gebruikers moeten tijdens het installatie proces ' python toevoegen aan uw pad ' selecteren.
1. Download en installeer [Visual Studio Code](https://code.visualstudio.com/Download).
1. Open Visual Studio Code en installeer de Python-extensie. Selecteer **Bestand** > **Voorkeuren** > **Extensies** in het menu. Zoek **python** en klik op **installeren**.

   ![De Python-extensie installeren](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Ook vanuit Visual Studio code installeert u het pakket speech SDK python vanaf de geïntegreerde opdracht regel:
   1. Open een Terminal (in de vervolg keuzelijsten **terminal** > **nieuwe terminal**)
   1. Voer in de terminal die wordt geopend de opdracht in `python -m pip install azure-cognitiveservices-speech`

Als u niet bekend bent met Visual Studio code, raadpleegt u de uitgebreide [documentatie over Visual Studio code](https://code.visualstudio.com/docs). Zie [Visual Studio code python-zelf studie](https://code.visualstudio.com/docs/python/python-tutorial)voor meer informatie over Visual Studio code en python.

## <a name="support-and-updates"></a>Ondersteuning en updates

Updates voor het Python Speech-SDK-pakket worden gedistribueerd via PyPI en aangekondigd in de [Releaseopmerkingen](~/articles/cognitive-services/speech-service/releasenotes.md).
Als een nieuwe versie beschikbaar is, kunt u een update naar deze versie uitvoeren met behulp van de opdracht `pip install --upgrade azure-cognitiveservices-speech`.
U kunt controleren welke versie momenteel is geïnstalleerd door de variabele `azure.cognitiveservices.speech.__version__` te bekijken.

Zie [opties voor ondersteuning en hulp](~/articles/cognitive-services/speech-service/support.md) als er een probleem is of als er een functie ontbreekt.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list.md)]
