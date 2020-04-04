---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: dd5dfa6db6b6855e377600a71a923c5be3557b60
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658894"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Een Azure-spraakbron maken<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=jre)
> * Zorg ervoor dat u toegang hebt tot een microfoon voor audio-opname

## <a name="source-code"></a>Broncode

Als u een nieuwe lege klasse wilt toevoegen aan uw Java-project, selecteert u **Bestand** > **Nieuwe** > **klasse**. Voer in het venster **Nieuwe Java-klasse**, in het veld **Pakket**, **speechsdk.quickstart** in en voer in het veld **Naam****Main** in.

![Schermopname van het venster Nieuwe Java-klasse](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

Vervang de inhoud van het *Main.java-bestand* door het volgende fragment:

[!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/src/speechsdk/quickstart/Main.java#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Code uitleg

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>App bouwen en uitvoeren

Druk op <kbd>F11</kbd>of selecteer **Foutopsporing** > **uitvoeren**.
De volgende 15 seconden aan spraakinvoer vanuit uw microfoon worden herkend en geregistreerd in het consolevenster.

![Schermafbeelding van console-uitvoer na geslaagde herkenning](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](../footer.md)]
