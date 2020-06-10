---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 64a6bf1fec61871d2787966a5b0af24d4f012032
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84637605"
---
:::row:::
    :::column span="3":::
        De Java SDK voor Android is verpakt als een <a href="https://developer.android.com/studio/projects/android-library" target="_blank">Aar (Android-bibliotheek <span class="docon docon-navigate-external x-hidden-focus"></span> ) </a>, waaronder de benodigde bibliotheken en de vereiste Android-machtigingen. Het wordt gehost in een Maven-opslag plaats op `https://csspeechstorage.blob.core.windows.net/maven/` als pakket `com.microsoft.cognitiveservices.speech:client-sdk:1.12.1` .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Als u het pakket wilt gebruiken uit uw Android Studio project, voert u de volgende wijzigingen uit:

1. Voeg in het bestand *Build. gradle* van project niveau het volgende toe aan de `repository` sectie:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. Voeg in het bestand *Build. gradle* van module niveau het volgende toe aan de `dependencies` sectie:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.12.1'
  ```

De Java SDK maakt ook deel uit van de [SDK voor spraak apparaten](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Aanvullende bronnen

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android-specifieke Java Quick Start-bron code<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Bron code voor Quick Start java runtime (JRE)<span class="docon docon-navigate-external x-hidden-focus"></span></a>