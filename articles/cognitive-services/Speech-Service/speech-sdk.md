---
title: Over de Speech SDK-Speech-Service
titleSuffix: Azure Cognitive Services
description: De speech Software Development Kit (SDK) biedt uw toepassingen systeem eigen toegang tot de functies van de spraak service, waardoor het eenvoudiger wordt om software te ontwikkelen. In dit artikel vindt u meer informatie over de SDK voor Windows, Linux en Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: f6446a2dbe3f111c9c617e78a0bdd98f6cea9153
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468775"
---
# <a name="about-the-speech-sdk"></a>Over de Speech SDK

De speech Software Development Kit (SDK) biedt uw toepassingen toegang tot de functies van de spraak Services, waardoor het eenvoudiger wordt om software met spraak functionaliteit te ontwikkelen. Op dit moment bieden de Sdk's toegang tot **spraak-naar-tekst**, **tekst-naar-spraak**, **spraak omzetting**, **intentie herkenning**en **bot Framework direct-lijn spraak kanaal**. Op de pagina documentatie- [invoer](https://aka.ms/csspeech)vindt u een algemeen overzicht van de mogelijkheden en ondersteunde platforms.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>De SDK downloaden

### <a name="windows"></a>Windows

Voor Windows ondersteunen we de volgende talen:

* C#(UWP en .NET) C++: u kunt verwijzen naar en de meest recente versie van het Speech SDK NuGet-pakket gebruiken. Het pakket bevat 32-bits en 64-bits-client bibliotheken en beheerde (.NET) bibliotheken. De SDK kan worden geïnstalleerd in Visual Studio met behulp van NuGet. Zoek naar **micro soft. CognitiveServices. speech**.

* Java: u kunt naar de meest recente versie van het Speech SDK maven-pakket verwijzen en gebruiken, dat alleen Windows x64 ondersteunt. Voeg `https://csspeechstorage.blob.core.windows.net/maven/` in uw Maven-project als een extra opslag plaats toe en referentie `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` als een afhankelijkheid.

### <a name="linux"></a>Linux

> [!NOTE]
> Momenteel ondersteunen we alleen Ubuntu 16,04, Ubuntu 18,04 en Debian 9 voor de volgende doel architecturen:
> - x86, x64 en ARM64 voor C++ ontwikkeling
> - x64 en ARM64 voor Java
> - x64 voor .NET core en python

Zorg ervoor dat de vereiste bibliotheken zijn geïnstalleerd door de volgende shell-opdrachten uit te voeren:

Op Ubuntu:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Op Debian 9:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

* C#: U kunt de meest recente versie van het Speech SDK NuGet-pakket raadplegen en gebruiken. Als u wilt verwijzen naar de SDK, voegt u de volgende pakket verwijzing toe aan uw project:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.7.0" />
  ```

* Java: u kunt verwijzen naar en de meest recente versie van het Speech SDK maven-pakket gebruiken. Voeg `https://csspeechstorage.blob.core.windows.net/maven/` in uw Maven-project als een extra opslag plaats toe en referentie `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` als een afhankelijkheid.

* C++: Down load de SDK als een [. tar-pakket](https://aka.ms/csspeech/linuxbinary) en pak de bestanden uit in een door u gewenste map. In de volgende tabel ziet u de structuur van de SDK:

  |Pad|Beschrijving|
  |-|-|
  |`license.md`|Licentie|
  |`ThirdPartyNotices.md`|Mededelingen van derden|
  |`include`|Header bestanden voor C enC++|
  |`lib/x64`|Systeem eigen x64-bibliotheek voor koppeling met uw toepassing|
  |`lib/x86`|Systeem eigen x86-bibliotheek voor koppeling met uw toepassing|

  Als u een toepassing wilt maken, kopieert of verplaatst u de vereiste binaire bestanden (en bibliotheken) naar uw ontwikkel omgeving. Neem deze op als vereist in uw bouw proces.

### <a name="android"></a>Android

De Java SDK voor Android is verpakt als een [Aar (Android-bibliotheek)](https://developer.android.com/studio/projects/android-library), waaronder de benodigde bibliotheken en de vereiste Android-machtigingen. Het wordt gehost in een Maven-opslag plaats op `https://csspeechstorage.blob.core.windows.net/maven/` als pakket `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`.

Als u het pakket wilt gebruiken uit uw Android Studio project, voert u de volgende wijzigingen uit:

* Voeg in het bestand build. gradle van project niveau het volgende toe aan de sectie `repository`:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* Voeg in het bestand build. gradle van module niveau het volgende toe aan de sectie `dependencies`:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

De Java SDK maakt ook deel uit van de [SDK voor spraak apparaten](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Volgende stappen

* [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
* [Zie spraak herkennen inC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
