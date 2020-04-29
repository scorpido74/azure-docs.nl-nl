---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 3613d190ef079d0e477d42b426a224d8e4dda7e6
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400709"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Een Azure-spraak resource maken<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Stel uw ontwikkel omgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * Zorg ervoor dat u toegang tot een microfoon hebt voor het vastleggen van audio

## <a name="source-code"></a>Broncode

Maak een bestand met de naam *QuickStart.py* en plak de volgende python-code hierin.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Uitleg bij code

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>App bouwen en uitvoeren

U bent nu klaar om de app te testen en de spraakherkennings functionaliteit te controleren met behulp van de speech service.

1. **Start uw app** -vanaf de opdracht regel, typt u:
    ```bash
    python quickstart.py
    ```
2. De **herkenning starten** : u wordt gevraagd om een woord groep in het Engels te spreken. Uw spraak wordt verzonden naar de spraak service, getranscribeerd als tekst en weer gegeven in de-console.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

