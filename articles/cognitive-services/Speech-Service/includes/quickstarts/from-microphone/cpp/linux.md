---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 07148941578749a632a6a7735d1e406ef9f81b9b
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658873"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Een Azure-spraakbron maken<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Stel uw ontwikkelomgeving in en maak een leeg project](../../../../quickstarts/setup-platform.md?tabs=linux)
> * Zorg ervoor dat u toegang hebt tot een microfoon voor audio-opname

## <a name="source-code"></a>Broncode

Maak een C++ bronbestand met de naam *helloworld.cpp*en plak de volgende code erin.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/linux/from-microphone/helloworld.cpp#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Code uitleg

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-the-app"></a>De app bouwen

> [!NOTE]
> Zorg ervoor dat u de onderstaande opdrachten als _één opdrachtregel_ invoert. De eenvoudigste manier om dat te doen is om de opdracht te kopiëren met behulp van de knop **Kopiëren** naast elke opdracht en deze vervolgens bij de shell-prompt te plakken.

* Voer op een **x64** (64-bits) systeem de volgende opdracht uit om de toepassing te compileren.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* Voer op een **x86** (32-bits) systeem de volgende opdracht uit om de toepassing te compileren.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
  ```

* Voer op een **ARM64-systeem** (64-bits) de volgende opdracht uit om de toepassing te bouwen.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/arm64" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>De app uitvoeren

1. Configureer het bibliotheekpad van het laadprogramma om te verwijzen naar de bibliotheek van de Speech SDK.

   * Voer op een **x64** (64-bits) systeem de volgende opdracht uit.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * Voer op een **x86** (32-bits) systeem de volgende opdracht uit.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

   * Voer op een **ARM64-systeem** (64-bits) de volgende opdracht in.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/arm64"
     ```

1. Voer de toepassing uit.

   ```sh
   ./helloworld
   ```

1. In het consolevenster wordt een prompt weergegeven waarin u wordt gevraagd om iets te zeggen. Spreek een Engelse woordgroep of zin in. De gesproken tekst wordt verzonden naar de Speech-service en getranscribeerd naar tekst, die in hetzelfde venster wordt weergegeven.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](../footer.md)]
