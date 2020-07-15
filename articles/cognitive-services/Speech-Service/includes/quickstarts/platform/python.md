---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: a7e47ebd9695b57163aacdfa5faf3ad82fc7e12d
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85805560"
---
In deze gids ontdekt u hoe u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) kunt installeren voor Python. Als u enkel de pakketnaam nodig heeft om zelf aan de slag te gaan, voer dan `pip install azure-cognitiveservices-speech` uit.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

- Het Python Speech-SDK-pakket is beschikbaar voor deze besturingssystemen:
  - Windows: x64 en x86
  - Mac: Mac OS X-versie 10.12 of hoger
  - Linux: Ubuntu 16.04/18.04, Debian 9, RHEL 7/8, CentOS 7/8 op x64

## <a name="prerequisites"></a>Vereisten

- Voor ondersteunde Linux-platformen moeten bepaalde bibliotheken zijn geïnstalleerd (`libssl` voor de ondersteuning van Secure Sockets Layer en `libasound2` voor de ondersteuning van geluid). Zoek uw distributie hieronder voor de opdracht waarmee u de correcte versies van deze bibliotheken kunt installeren.

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

  - Voer op RHEL/CentOS de volgende opdrachten uit om de vereiste pakketten te installeren:

    ```sh
    sudo yum update
    sudo yum install alsa-lib openssl python3
    ```

> [!NOTE]
> - Volg op RHEL/CentOS 7 de instructies op [RHEL/CentOS 7 configureren voor Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Volg op RHEL/CentOS 8 de instructies op [OpenSSL configureren voor Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- In Windows hebt u het [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) voor uw platform nodig. Houd er rekening mee dat als u dit voor het eerst installeert, Windows opnieuw moet worden opgestart voordat u doorgaat met deze gids.
- En als laatste heeft u [Python 3.5 tot 3.8](https://www.python.org/downloads/) nodig. Om uw installatie te controleren, opent u een opdrachtprompt, typt u de opdracht `python --version` in en controleert u het resultaat. Als de installatie in orde is, krijgt u het antwoord 'Python 3.5.1' of iets soortgelijk.

## <a name="install-the-speech-sdk-from-pypi"></a>De Speech-SDK installeren van PyPI

Als u uw eigen omgeving of build-hulpprogramma's gebruikt, voert u de volgende opdracht uit om de Speech SDK te installeren vanuit [PyPI](https://pypi.org/). Gebruikers van Visual Studio Code kunnen de volgende subsectie van de begeleide installatie overslaan.

```sh
pip install azure-cognitiveservices-speech
```

Als u macOS gebruikt, dan moet u mogelijk de volgende opdracht uitvoeren om de bovenstaande `pip`-opdracht te laten werken:

```sh
python3 -m pip install --upgrade pip
```

Zodra u `pip` heeft gebruikt om `azure-cognitiveservices-speech` te installeren, kunt u Speech SDK gebruiken door de naamruimte in uw Python-projecten te importeren.

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>De Speech-SDK installeren met Visual Studio Code

1. Download en installeer de meest recente ondersteunde versie van [Python](https://www.python.org/downloads/) voor uw platform, 3.5 tot 3.8.
   - Windows-gebruikers moeten tijdens het installatieproces 'Python toevoegen aan uw PAD' selecteren.
1. Download en installeer [Visual Studio Code](https://code.visualstudio.com/Download).
1. Open Visual Studio Code en installeer de Python-extensie. Selecteer **Bestand** > **Voorkeuren** > **Extensies** in het menu. Zoek **Python** en klik op **Installeren**.

   ![De Python-extensie installeren](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. U kunt het Python Speech-SDK-pakket ook installeren vanuit Visual Studio Code met de geïntegreerde opdrachtregel:
   1. Open een terminal (in de vervolgkeuzelijsten **Terminal** > **Nieuwe terminal**)
   1. Voer in de terminal die wordt geopend de opdracht `python -m pip install azure-cognitiveservices-speech` in

Als Visual Studio Code nieuw is voor u, bekijk dan de uitgebreide [Documentatie voor Visual Studio Code](https://code.visualstudio.com/docs). Zie [Zelfstudie voor Visual Studio Code Python](https://code.visualstudio.com/docs/python/python-tutorial) voor meer informatie over Visual Studio Code en Python.

## <a name="support-and-updates"></a>Ondersteuning en updates

Updates voor het Python Speech-SDK-pakket worden gedistribueerd via PyPI en aangekondigd in de [Releaseopmerkingen](~/articles/cognitive-services/speech-service/releasenotes.md).
Als een nieuwe versie beschikbaar is, kunt u een update naar deze versie uitvoeren met behulp van de opdracht `pip install --upgrade azure-cognitiveservices-speech`.
U kunt controleren welke versie momenteel is geïnstalleerd door de variabele `azure.cognitiveservices.speech.__version__` te bekijken.

Zie [opties voor ondersteuning en hulp](~/articles/cognitive-services/speech-service/support.md) als er een probleem is of als er een functie ontbreekt.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list.md)]
