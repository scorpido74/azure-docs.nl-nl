---
title: Apps ontwikkelen met de Speech SDK-Speech-Service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het implementeren van een toepassing die gebruikmaakt van de Speech SDK op ondersteunde platforms.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 9507428e63b337b3d8419a833d03d081d494c522
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330801"
---
# <a name="ship-an-application"></a>Een toepassing verzenden

Bekijk de [Speech SDK-licentie](https://aka.ms/csspeech/license201809), evenals de [kennisgevingen van software van derden](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) wanneer u de Azure Cognitive Services Speech SDK distribueert. Lees ook de [privacyverklaring van micro soft](https://aka.ms/csspeech/privacy).

Afhankelijk van het platform, er verschillende afhankelijkheden bestaan voor het uitvoeren van uw toepassing.

## <a name="windows"></a>Windows

De Cognitive Services Speech SDK is getest op Windows 10 en Windows Server 2016.

De Cognitive Services Speech SDK vereist [micro soft Visual C++ Redistributable voor Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) op het systeem. U kunt installatie Programma's voor de nieuwste versie van de `Microsoft Visual C++ Redistributable for Visual Studio 2019` hier downloaden:

- [-](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x86](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Als uw toepassing beheerde code gebruikt, is de `.NET Framework 4.6.1` of later vereist op de doel computer.

De Media Foundation-bibliotheken moeten worden geïnstalleerd voor de invoer van de microfoon. Deze bibliotheken maken deel uit van Windows 10 en Windows Server 2016. Het is mogelijk het gebruik van de spraak-SDK zonder deze bibliotheken, zolang een microfoon niet wordt gebruikt als de audio-invoerapparaat.

De vereiste spraak SDK-bestanden kunnen worden geïmplementeerd in dezelfde map als uw toepassing. Op deze manier uw toepassing hebben rechtstreeks toegang tot de bibliotheken. Zorg ervoor dat u selecteert de juiste versie (Win32/x64) die overeenkomt met uw toepassing.

| Naam | Functie |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core-SDK, vereist voor de systeemeigen en beheerde implementatie |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | vereist voor de beheerde implementatie                      |

> [!NOTE]
> Vanaf de release 1.3.0 is de bestands `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (geleverd in eerdere releases) niet meer nodig. De functionaliteit is nu geïntegreerd in de core-SDK.

> [!NOTE]
> Controleer voor het project van de Windows Forms C# -App (.NET Framework) of de bibliotheken zijn opgenomen in de implementatie-instellingen van uw project. U kunt dit controleren onder `Properties -> Publish Section`. Klik op de knop `Application Files` en zoek de bijbehorende bibliotheken in de schuif lijst omlaag. Zorg ervoor dat de waarde is ingesteld op `Included`. Visual Studio neemt het bestand op wanneer het project wordt gepubliceerd/geïmplementeerd.

## <a name="linux"></a>Linux

De Speech SDK ondersteunt momenteel de Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8-distributies.
Voor een systeem eigen toepassing moet u de Speech SDK-bibliotheek verzenden `libMicrosoft.CognitiveServices.Speech.core.so`.
Zorg ervoor dat u selecteert u de versie (x86, x64) die overeenkomt met uw toepassing. Afhankelijk van de Linux-versie moet u ook mogelijk om op te nemen van de volgende afhankelijkheden:

- De gedeelde bibliotheken van de GNU C-bibliotheek (met inbegrip van de programmeer bibliotheek POSIX-threads, `libpthreads`)
- De OpenSSL-bibliotheek (`libssl.so.1.0.0` of `libssl.so.1.0.2`)
- De gedeelde bibliotheek voor ALSA-toepassingen (`libasound.so.2`)

Op Ubuntu moeten de GNU C-bibliotheken standaard al zijn geïnstalleerd. De laatste drie kan worden geïnstalleerd met behulp van deze opdrachten:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Op Debian 9 installeert u deze pakketten:

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
> Volg in RHEL/CentOS 8 de instructies voor het [configureren van openssl voor Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

## <a name="next-steps"></a>Volgende stappen

- [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
- [Zie spraak herkennen inC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
