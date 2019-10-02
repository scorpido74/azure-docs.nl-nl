---
title: Over de Speech SDK-Speech-Service
titleSuffix: Azure Cognitive Services
description: De speech Software Development Kit (SDK) biedt uw toepassingen systeem eigen toegang tot de functies van de spraak service, waardoor het eenvoudiger wordt om software te ontwikkelen. In dit artikel biedt aanvullende informatie over de SDK voor Windows, Linux- en Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: ee505cc78c16d85640c7e13541272f1bafe30061
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71800007"
---
# <a name="about-the-speech-sdk"></a>Over de Speech SDK

De speech Software Development Kit (SDK) biedt uw toepassingen toegang tot de functies van de spraak Services, waardoor het eenvoudiger wordt om software met spraak functionaliteit te ontwikkelen. Op dit moment bieden de Sdk's toegang tot **spraak-naar-tekst**, **tekst-naar-spraak**, **spraak omzetting**, **intentie herkenning**en **bot Framework direct-lijn spraak kanaal**. Op de pagina documentatie- [invoer](https://aka.ms/csspeech)vindt u een algemeen overzicht van de mogelijkheden en ondersteunde platforms.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Download de SDK

### <a name="windows"></a>Windows

Bij Windows, wordt de volgende talen ondersteund:

* C#(UWP en .NET), C++: U kunt de meest recente versie van het Speech SDK NuGet-pakket raadplegen en gebruiken. Het pakket bevat 32-bits en 64-bits-clientbibliotheken en beheerde (.NET)-bibliotheken. De SDK kan worden geïnstalleerd in Visual Studio met behulp van NuGet. Zoeken naar **Microsoft.CognitiveServices.Speech**.

* Java: U kunt de meest recente versie van het Speech SDK maven-pakket raadplegen en gebruiken dat alleen Windows x64 ondersteunt. Voeg in uw Maven-project `https://csspeechstorage.blob.core.windows.net/maven/` als een aanvullende opslagplaats en een verwijzing `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` als een afhankelijkheid.

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

* C#: U kunt de meest recente versie van het Speech SDK NuGet-pakket raadplegen en gebruiken. Om te verwijzen naar de SDK, het volgende pakketverwijzing toevoegen aan uw project:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.7.0" />
  ```

* Java: U kunt de meest recente versie van het Speech SDK maven-pakket raadplegen en gebruiken. Voeg in uw Maven-project `https://csspeechstorage.blob.core.windows.net/maven/` als een aanvullende opslagplaats en een verwijzing `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` als een afhankelijkheid.

* C++: Down load de SDK als een [. tar-pakket](https://aka.ms/csspeech/linuxbinary) en pak de bestanden uit in een door u gewenste map. De volgende tabel bevat de SDK-mapstructuur:

  |Pad|Description|
  |-|-|
  |`license.md`|Licentie|
  |`ThirdPartyNotices.md`|Mededelingen van derden|
  |`include`|Header-bestanden voor C en C++|
  |`lib/x64`|Systeemeigen x64 bibliotheek voor het koppelen met uw toepassing|
  |`lib/x86`|Systeemeigen x86 bibliotheek voor het koppelen met uw toepassing|

  Voor het maken van een toepassing, kopiëren of verplaatsen van de vereiste binaire bestanden (en -bibliotheken) in uw ontwikkelomgeving. Deze waar nodig in uw build-proces opnemen.

### <a name="android"></a>Android

De Java SDK voor Android is verpakt als een [Aar (Android-bibliotheek)](https://developer.android.com/studio/projects/android-library), waaronder de benodigde bibliotheken en de vereiste Android-machtigingen. Deze wordt gehost in een Maven-opslagplaats op `https://csspeechstorage.blob.core.windows.net/maven/` als pakket `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`.

Het pakket van uw Android Studio-project gebruiken, moet u de volgende wijzigingen aanbrengen:

* In het niveau van het project build.gradle-bestand, het volgende toevoegen aan de `repository` sectie:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* In de module op serverniveau build.gradle-bestand, het volgende toevoegen aan de `dependencies` sectie:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

De Java-SDK is ook deel uit van de [spraak Devices SDK](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Volgende stappen

* [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
* [Zie voor het herkennen van gesproken tekst in C#](quickstart-csharp-dotnet-windows.md)
