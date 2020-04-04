---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 59835890a5baaf050868951ea683fd97b795ce3e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656562"
---
:::row:::
    :::column span="3":::
        De Java SDK voor Android is verpakt als een <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR (Android <span class="docon docon-navigate-external x-hidden-focus"> </span>Library), </a>die de nodige bibliotheken en vereiste Android-machtigingen bevat. Het wordt gehost in een Maven repository op `https://csspeechstorage.blob.core.windows.net/maven/` als pakket. `com.microsoft.cognitiveservices.speech:client-sdk:1.11.0`
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Als u het pakket wilt gebruiken vanuit uw Android Studio-project, voert u de volgende wijzigingen aan:

1. Voeg in het bestand *build.gradle op* projectniveau het volgende toe aan de `repository` sectie:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. Voeg in het bestand *build.gradle* op moduleniveau het volgende toe aan de `dependencies` sectie:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
  ```

De Java SDK is ook onderdeel van de [Speech Devices SDK.](../speech-devices-sdk.md)

#### <a name="additional-resources"></a>Aanvullende bronnen

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android specifieke Java quickstart broncode<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Java Runtime (JRE) quickstart broncode<span class="docon docon-navigate-external x-hidden-focus"></span></a>