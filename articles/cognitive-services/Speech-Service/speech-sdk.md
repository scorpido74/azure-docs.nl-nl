---
title: Over de Speech SDK - Spraakservice
titleSuffix: Azure Cognitive Services
description: De Speech Software Development Kit (SDK) geeft uw applicaties native toegang tot de functies van de Speech-service, waardoor het eenvoudiger wordt om software te ontwikkelen. In dit artikel vindt u meer informatie over de SDK voor Windows, Linux en Android.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: dapine
ms.openlocfilehash: 984d2dfe07faa22756b4be167aa86a69806b1a84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331090"
---
# <a name="about-the-speech-sdk"></a>Info over de Speech-SDK

De Speech Software Development Kit (SDK) geeft uw applicaties toegang tot de functies van de Speech-service, waardoor het eenvoudiger wordt om spraakgestuurde software te ontwikkelen. Momenteel bieden de SDK's toegang tot **spraak-naar-tekst,** **tekst-naar-spraak,** **spraakvertaling,** **intentieherkenning**en **Bot Framework's Direct Line Speech-kanaal**.

U eenvoudig audio opnemen vanuit een microfoon, lezen vanuit een stream of audiobestanden openen vanuit de opslag met de Speech SDK. De Speech SDK ondersteunt WAV/PCM 16-bits, 16 kHz/8 kHz, single-channel audio voor spraakherkenning. Extra audio-indelingen worden ondersteund met het [restpunt spraak-naar-tekst](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) of de [batchtranscriptieservice.](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)

Een algemeen overzicht over de mogelijkheden en ondersteunde platforms is te vinden op de [pagina met documentatieinvoer.](https://aka.ms/csspeech)

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Download de SDK

# <a name="windows"></a>[Windows](#tab/windows)

> [!WARNING]
> De Speech SDK ondersteunt Windows 10- of latere versies. Eerdere Windows-versies **worden niet ondersteund.**

Voor Windows ondersteunen we de volgende talen:

* C# (UWP en .NET), C++: U verwijzen naar en gebruik maken van de nieuwste versie van ons Speech SDK NuGet-pakket. Het pakket bevat 32-bits en 64-bits clientbibliotheken en beheerde (.NET) bibliotheken. De SDK kan worden geïnstalleerd in Visual Studio met Behulp van NuGet, [Microsoft.CognitiveServices.Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech).

* Java: U verwijzen naar en gebruik maken van de nieuwste versie van onze Speech SDK Maven pakket, die alleen Windows x64 ondersteunt. Voeg in uw Maven-project toe `https://csspeechstorage.blob.core.windows.net/maven/` als `com.microsoft.cognitiveservices.speech:client-sdk:1.8.0` extra opslagplaats en als referentie als afhankelijkheid.

# <a name="linux"></a>[Linux](#tab/linux)

> [!NOTE]
> Momenteel ondersteunen we alleen Ubuntu 16.04, Ubuntu 18.04, Debian 9, Red Hat Enterprise Linux (RHEL) 8 en CentOS 8 op de volgende doelarchitecturen:
> - x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) en ARM64 (Debian/Ubuntu) voor C++ ontwikkeling
> - x64, ARM32 (Debian/Ubuntu) en ARM64 (Debian/Ubuntu) voor Java
> - x64 voor .NET Core en Python

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

Op RHEL/CentOS 8:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> Volg op RHEL/CentOS 8 de instructies voor [het configureren van OpenSSL voor Linux.](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)

* C#: U verwijzen naar en gebruik maken van de nieuwste versie van ons Speech SDK NuGet-pakket. Als u naar de SDK wilt verwijzen, voegt u de volgende pakketverwijzing toe aan uw project:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java: U de nieuwste versie van ons Speech SDK Maven pakket nagaan en gebruiken. Voeg in uw Maven-project toe `https://csspeechstorage.blob.core.windows.net/maven/` als `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` extra opslagplaats en als referentie als afhankelijkheid.

* C++: Download de SDK als [een .tar-pakket](https://aka.ms/csspeech/linuxbinary) en pak de bestanden uit in een map naar keuze. In de volgende tabel ziet u de structuur van de SDK-map:

  |Pad|Beschrijving|
  |-|-|
  |`license.md`|Licentie|
  |`ThirdPartyNotices.md`|Mededelingen van derden|
  |`include`|Koptekstbestanden voor C en C++|
  |`lib/x64`|Native x64-bibliotheek voor koppeling met uw toepassing|
  |`lib/x86`|Native x86-bibliotheek voor koppeling met uw toepassing|

  Als u een toepassing wilt maken, kopieert of verplaatst u de vereiste binaire bestanden (en bibliotheken) naar uw ontwikkelomgeving. Neem ze op zoals vereist in uw bouwproces.

# <a name="android"></a>[Android](#tab/android)

De Java SDK voor Android is verpakt als een [AAR (Android Library),](https://developer.android.com/studio/projects/android-library)die de nodige bibliotheken en vereiste Android-machtigingen bevat. Het wordt gehost in een Maven repository op `https://csspeechstorage.blob.core.windows.net/maven/` als pakket. `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`

Als u het pakket wilt gebruiken vanuit uw Android Studio-project, voert u de volgende wijzigingen aan:

* Voeg in het bestand build.gradle op projectniveau het volgende toe aan de `repository` sectie:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* Voeg in het bestand build.gradle op moduleniveau het volgende toe aan de `dependencies` sectie:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

De Java SDK is ook onderdeel van de [Speech Devices SDK.](speech-devices-sdk.md)

---

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Volgende stappen

* [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
* [Bekijk hoe u spraak in C herkent #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
