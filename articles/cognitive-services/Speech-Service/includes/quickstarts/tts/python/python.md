---
title: 'Quick Start: een synthese van spraak, python-Speech-Service'
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van spraak in python met behulp van de Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/14/2019
ms.author: yulili
ms.openlocfilehash: 990e795d0331df41ff098ed498508d6ecfd58f0e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818475"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een Azure-spraak resource maken](../../../../get-started.md)
> * [Uw ontwikkel omgeving instellen](../../../../quickstarts/setup-platform.md)
> * [Een leeg voorbeeld project maken](../../../../quickstarts/create-project.md)
```

## Support and updates

Updates to the Speech SDK Python package are distributed via PyPI and announced in the [Release notes](~/articles/cognitive-services/Speech-Service/releasenotes.md).
If a new version is available, you can update to it with the command `pip install --upgrade azure-cognitiveservices-speech`.
Check which version is currently installed by inspecting the `azure.cognitiveservices.speech.__version__` variable.

If you have a problem, or you're missing a feature, see [Support and help options](~/articles/cognitive-services/Speech-Service/support.md).

## Create a Python application that uses the Speech SDK

### Run the sample

You can copy the [sample code](#sample-code) from this quickstart to a source file `quickstart.py` and run it in your IDE or in the console:

```sh
python quickstart.py
```

Of u kunt deze quickstart-zelfstudie downloaden als een [Jupyter](https://jupyter.org)-notebook uit de [opslagplaats met Speech-SDK-voorbeelden](https://aka.ms/csspeech/samples) en de quickstart uitvoeren als een notitieblok.

### <a name="sample-code"></a>Voorbeeldcode

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/text-to-speech/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>De Speech-SDK installeren en gebruiken met Visual Studio Code

1. Download en installeer een 64-bits versie (3.5 of hoger) van [Python](https://www.python.org/downloads/) op de computer.
1. Download en installeer [Visual Studio Code](https://code.visualstudio.com/Download).
1. Open Visual Studio Code en installeer de Python-extensie. Selecteer **Bestand** > **Voorkeuren** > **Extensies** in het menu. Zoek naar **Python**.

   ![De Python-extensie installeren](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Maak een map om daar het project in op te slaan. Dat kunt u bijvoorbeeld doen met Windows Verkenner.
1. Selecteer in Visual Studio Code het pictogram **Bestand**. Open vervolgens de map die u hebt gemaakt.

   ![Een map openen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Maak een nieuw Python-bronbestand, `speechsdk.py`, door het pictogram Nieuw bestand te selecteren.

   ![Een bestand maken](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Kopieer en plak de [Python-code](#sample-code) en sla deze op in het zojuist gemaakte bestand.
1. Voeg de gegevens van uw Speech Service-abonnement in.
1. Als er een Python-interpreter is geselecteerd, wordt deze onder aan het venster weergegeven, links van de statusbalk.
   Anders wordt er een lijst met beschikbare Python-interpreters geopend. Open het opdracht palet (CTRL + SHIFT + P) en voer **python in: Select interpreter**. Kies de juiste versie.
1. U kunt het Python Speech-SDK-pakket installeren vanuit Visual Studio Code. Doe dat als het pakket nog niet is geïnstalleerd voor de Python-interpreter die u hebt geselecteerd.
   Open een terminal voor het installeren van het Speech-SDK-pakket. Breng het opdracht palet opnieuw aan (CTRL + SHIFT + P) en voer de **volgende Terminal in: nieuwe geïntegreerde terminal maken**.
   Voer in de terminal die wordt geopend, de opdracht `python -m pip install azure-cognitiveservices-speech` in, of de juiste opdracht voor uw systeem.
1. Als u de voorbeeldcode wilt uitvoeren, klikt u met de rechtermuisknop ergens in de editor. Selecteer **Python-bestand uitvoeren in de Terminal**.
   Typ een tekst wanneer u hierom wordt gevraagd. De gesynthesizerde audio wordt binnenkort afgespeeld.

   ![Een voorbeeld uitvoeren](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run-tts.png)

Als er zich problemen voordoen met deze instructies, raadpleegt u de uitgebreidere [Python-zelfstudie voor Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste spraak voorbeelden vastleggen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
