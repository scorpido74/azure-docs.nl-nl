---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: d872f6b53d9376a6be962d6f4f13ee1b92b0e0a7
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671149"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Een Azure-spraakbron maken<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)
> * Zorg ervoor dat u toegang hebt tot een microfoon voor audio-opname

## <a name="source-code"></a>Broncode

Maak een C++ bronbestand met de naam *helloworld.cpp*en plak de volgende code erin.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Code uitleg

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>App bouwen en uitvoeren

1. Selecteer op de menubalk **Build** > **Build Solution** om de toepassing te bouwen. De code moet nu zonder fouten worden gecompileerd.

1. Kies **Foutopsporing debuggen** > **(of** druk op <kbd>F5)</kbd>om de **helloworld-toepassing** te starten.

1. Spreek een Engelse woordgroep of zin in. De toepassing verzendt uw toespraak naar de spraakservice, die overschrijft naar tekst en deze terugstuurt naar de toepassing voor weergave.

   ![Console-uitvoer na succesvolle herkenning](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](../footer.md)]