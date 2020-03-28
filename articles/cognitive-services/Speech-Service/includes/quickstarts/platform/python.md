---
title: 'Snelstart: SpraakSDK voor Python-platformsetup - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Gebruik deze handleiding om uw platform in te stellen voor het gebruik van Python met de Spraakservice SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/09/2019
ms.author: erhopf
ms.openlocfilehash: 458a6940ce214ef1931a2cc9ee95f2cb5ca16779
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925227"
---
In deze handleiding ziet u hoe u de [SpraakSDK](~/articles/cognitive-services/speech-service/speech-sdk.md) voor Python installeert. Als je gewoon wilt dat de naam van `pip install azure-cognitiveservices-speech`het pakket zelf aan de slag gaat, voer dan uit.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

- Het Python Speech-SDK-pakket is beschikbaar voor deze besturingssystemen:
  - Windows: x64 en x86
  - Mac: macOS X versie 10.12 of hoger
  - Linux: Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8 op x64

## <a name="prerequisites"></a>Vereisten

- Ondersteunde Linux-platforms vereisen bepaalde geïnstalleerde`libssl` bibliotheken (voor ondersteuning `libasound2` van beveiligde sockets laag en voor geluidsondersteuning). Raadpleeg hieronder uw distributie voor de opdrachten die nodig zijn om de juiste versies van deze bibliotheken te installeren.

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
> Volg op RHEL/CentOS 8 de instructies voor [het configureren van OpenSSL voor Linux.](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)

- In Windows heb je de [Microsoft Visual C++ Redistributable voor Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) nodig voor je platform. Houd er rekening mee dat het installeren van deze voor de eerste keer kan vereisen dat u Windows opnieuw opstart voordat u verder gaat met deze handleiding.
- En tot slot heb je [Python 3,5 tot 3,8](https://www.python.org/downloads/)nodig. Als u uw installatie wilt controleren, `python --version` opent u een opdrachtprompt en typt u de opdracht en controleert u het resultaat. Als het goed is geïnstalleerd, krijg je een antwoord "Python 3.5.1" of iets dergelijks.

## <a name="install-the-speech-sdk-from-pypi"></a>De SpraakSDK van PyPI installeren

Als u uw eigen omgeving gebruikt of hulpprogramma's bouwt, voert u de volgende opdracht uit om de SpraakSDK van [PyPI](https://pypi.org/)te installeren. Voor gebruikers van Visual Studio Code gaat u naar de volgende subsectie voor begeleide installatie.

```sh
pip install azure-cognitiveservices-speech
```

Als u macOS bevindt, moet u mogelijk de `pip` volgende opdracht uitvoeren om de bovenstaande opdracht te laten werken:

```sh
python3 -m pip install --upgrade pip
```

Zodra u de `pip` spraaksdk `azure-cognitiveservices-speech`hebt geïnstalleerd, u de SpraakSDK gebruiken door de naamruimte in uw Python-projecten te importeren.

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>De SpraakSDK installeren met Visual Studio Code

1. Download en installeer de nieuwste ondersteunde versie van [Python](https://www.python.org/downloads/) voor uw platform, 3,5 tot 3,8.
   - Windows-gebruikers zorgen ervoor dat u tijdens het installatieproces 'Python toevoegen aan uw PATH' selecteert.
1. Download en installeer [Visual Studio Code](https://code.visualstudio.com/Download).
1. Open Visual Studio Code en installeer de Python-extensie. Selecteer**Extensies** **voor bestandsvoorkeuren** > **Preferences** > in het menu. Zoek naar **Python** en klik op **Installeren**.

   ![De Python-extensie installeren](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. Installeer ook vanuit Visual Studio Code het Speech SDK Python-pakket vanaf de geïntegreerde opdrachtregel:
   1. Open een terminal (vanuit de vervolgkeuzemenu's, **Terminal** > **New Terminal)**
   1. Voer in de terminal die wordt geopend, de opdracht in`python -m pip install azure-cognitiveservices-speech`

Als u nieuw bent in Visual Studio Code, raadpleegt u de uitgebreidere Documentatie van de [Visuele StudioCode](https://code.visualstudio.com/docs). Zie [Visual Studio Code Python-zelfstudie](https://code.visualstudio.com/docs/python/python-tutorial)voor meer informatie over Visual Studio Code en Python.

## <a name="support-and-updates"></a>Ondersteuning en updates

Updates voor het Python Speech-SDK-pakket worden gedistribueerd via PyPI en aangekondigd in de [Releaseopmerkingen](~/articles/cognitive-services/speech-service/releasenotes.md).
Als een nieuwe versie beschikbaar is, kunt u een update naar deze versie uitvoeren met behulp van de opdracht `pip install --upgrade azure-cognitiveservices-speech`.
U kunt controleren welke versie momenteel is geïnstalleerd door de variabele `azure.cognitiveservices.speech.__version__` te bekijken.

Zie [opties voor ondersteuning en hulp](~/articles/cognitive-services/speech-service/support.md) als er een probleem is of als er een functie ontbreekt.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list.md)]
