---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: f26f0ab6da398dcdee307f89b27cca780d08af85
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85839029"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Een resource voor de Azure Speech-service maken<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * Zorg ervoor dat u toegang tot een microfoon hebt voor het vastleggen van audio

## <a name="source-code"></a>Broncode

Maak een bestand met de naam *quickstart.py* en plak de volgende Python-code erin.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Uitleg bij de code

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Een app bouwen en uitvoeren

U bent er nu klaar voor om de spraakherkenning van de Speech-service te testen. 

Als u dit in macOS doet en dit de eerste Python-app is die u bouwt waarbij gebruik wordt gemaakt van een microfoon, moet u waarschijnlijk Terminal toegang geven tot de microfoon. Open **Systeeminstellingen** en selecteer **Beveiliging en privacy**. Selecteer vervolgens **Privacy** en zoek **Microfoon** in de lijst. Selecteer tot slot **Terminal** en sla uw wijzigingen op. 

1. **App starten**: typ het volgende in de opdrachtregel:
    ```bash
    python quickstart.py
    ```
2. **Herkenning starten**: u wordt gevraagd om een woordgroep uit te spreken in het Engels. Uw spraak wordt verzonden naar de Speech-service, getranscribeerd als tekst en weergegeven in de console.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
