---
title: Apps ontwikkelen met de SpraakSDK - Spraakservice
titleSuffix: Azure Cognitive Services
description: Meer informatie over het implementeren van een toepassing die de SpraakSDK gebruikt op ondersteunde platforms.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330801"
---
# <a name="ship-an-application"></a>Een toepassing verzenden

Let op de [Speech SDK-licentie](https://aka.ms/csspeech/license201809)en de [softwaremeldingen van derden](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) wanneer u de Azure Cognitive Services Speech SDK distribueert. Bekijk ook de [Privacyverklaring van Microsoft](https://aka.ms/csspeech/privacy).

Afhankelijk van het platform bestaan er verschillende afhankelijkheden om uw toepassing uit te voeren.

## <a name="windows"></a>Windows

De Cognitive Services Speech SDK is getest op Windows 10 en op Windows Server 2016.

De Cognitive Services Speech SDK vereist de [Microsoft Visual C++ Redistributable voor Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) op het systeem. U installateurs downloaden voor de `Microsoft Visual C++ Redistributable for Visual Studio 2019` nieuwste versie van de hier:

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Als uw toepassing beheerde `.NET Framework 4.6.1` code gebruikt, is de of later vereist op de doelmachine.

Voor microfooninvoer moeten de bibliotheken van de Media Foundation worden geïnstalleerd. Deze bibliotheken maken deel uit van Windows 10 en Windows Server 2016. Het is mogelijk om de Speech SDK te gebruiken zonder deze bibliotheken, zolang een microfoon niet wordt gebruikt als het audio-invoerapparaat.

De vereiste Speech SDK-bestanden kunnen worden geïmplementeerd in dezelfde map als uw toepassing. Op deze manier heeft uw toepassing rechtstreeks toegang tot de bibliotheken. Zorg ervoor dat u de juiste versie (Win32/x64) selecteert die overeenkomt met uw toepassing.

| Name | Functie |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core SDK, vereist voor native en beheerde implementatie |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Vereist voor beheerde implementatie                      |

> [!NOTE]
> Te beginnen met de release 1.3.0 het bestand `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (verzonden in eerdere releases) is niet meer nodig. De functionaliteit is nu geïntegreerd in de core SDK.

> [!NOTE]
> Controleer bij het Project Windows Forms App (.NET Framework) C# of de bibliotheken zijn opgenomen in de implementatie-instellingen van uw project. U dit `Properties -> Publish Section`hieronder controleren. Klik `Application Files` op de knop en zoek overeenkomstige bibliotheken in de lijst scroll down. Controleer of de waarde `Included`is ingesteld op . Visual Studio bevat het bestand wanneer het project wordt gepubliceerd/geïmplementeerd.

## <a name="linux"></a>Linux

De Speech SDK ondersteunt momenteel de Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8 distributies.
Voor een native toepassing moet u de `libMicrosoft.CognitiveServices.Speech.core.so`Speech SDK-bibliotheek verzenden.
Zorg ervoor dat u de versie (x86, x64) selecteert die overeenkomt met uw toepassing. Afhankelijk van de Linux-versie moet u mogelijk ook de volgende afhankelijkheden opnemen:

- De gedeelde bibliotheken van de GNU C-bibliotheek (waaronder `libpthreads`de POSIX Threads Programmeerbibliotheek, )
- De OpenSSL-bibliotheek (`libssl.so.1.0.0` of `libssl.so.1.0.2`)
- De gedeelde bibliotheek voor`libasound.so.2`ALSA-toepassingen ( )

Op Ubuntu moeten de GNU C-bibliotheken al standaard worden geïnstalleerd. De laatste drie kunnen worden geïnstalleerd met behulp van deze opdrachten:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Op Debian 9 installeer deze pakketten:

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

## <a name="next-steps"></a>Volgende stappen

- [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
- [Bekijk hoe u spraak in C herkent #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
