---
title: Apps ontwikkelen met de Speech SDK-Speech-Service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van apps met behulp van de Speech SDK.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.custom: seodec18
ms.openlocfilehash: 166ae00085f07ef24d746b60947a31e7680a0f00
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491011"
---
# <a name="ship-an-application"></a>Een toepassing verzenden

Bekijk de [Speech SDK-licentie](https://aka.ms/csspeech/license201809), evenals de [kennisgevingen van software van derden](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) wanneer u de Azure Cognitive Services Speech SDK distribueert. Lees ook de [privacyverklaring van micro soft](https://aka.ms/csspeech/privacy).

Afhankelijk van het platform bestaan er verschillende afhankelijkheden voor het uitvoeren van uw toepassing.

## <a name="windows"></a>Windows

De Cognitive Services Speech SDK wordt getest op Windows 10 en op Windows Server 2016.

De Cognitive Services Speech SDK vereist [micro soft Visual C++ Redistributable voor Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) op het systeem. U kunt installatie Programma's voor de nieuwste versie van de `Microsoft Visual C++ Redistributable for Visual Studio 2019` hier downloaden:

- [-](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x86](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Als uw toepassing beheerde code gebruikt, is de `.NET Framework 4.6.1` of later vereist op de doel computer.

Voor invoer van de microfoon moeten de Media Foundation-bibliotheken zijn geïnstalleerd. Deze bibliotheken maken deel uit van Windows 10 en Windows Server 2016. Het is mogelijk om de Speech SDK te gebruiken zonder deze bibliotheken, zolang een microfoon niet wordt gebruikt als het apparaat voor audio-invoer.

De vereiste spraak-SDK-bestanden kunnen worden geïmplementeerd in dezelfde map als uw toepassing. Op deze manier kan uw toepassing rechtstreeks toegang krijgen tot de bibliotheken. Zorg ervoor dat u de juiste versie (Win32/x64) selecteert die overeenkomt met uw toepassing.

| Naam | Functie
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core-SDK, vereist voor systeem eigen en beheerde implementatie
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Vereist voor beheerde implementatie

>[!NOTE]
> Vanaf de release 1.3.0 is de bestands `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (geleverd in eerdere releases) niet meer nodig. De functionaliteit is nu geïntegreerd in de core-SDK.

>[!NOTE]
> Controleer voor het project van de Windows Forms C# -App (.NET Framework) of de bibliotheken zijn opgenomen in de implementatie-instellingen van uw project. U kunt dit controleren onder `Properties -> Publish Section`. Klik op de knop `Application Files` en zoek de bijbehorende bibliotheken in de schuif lijst omlaag. Zorg ervoor dat de waarde is ingesteld op `Included`. Visual Studio neemt het bestand op wanneer het project wordt gepubliceerd/geïmplementeerd.

## <a name="linux"></a>Linux

De Speech SDK ondersteunt momenteel de distributies Ubuntu 16,04, Ubuntu 18,04 en Debian 9.
Voor een systeem eigen toepassing moet u de Speech SDK-bibliotheek verzenden `libMicrosoft.CognitiveServices.Speech.core.so`.
Zorg ervoor dat u de versie (x86, x64) selecteert die overeenkomt met uw toepassing. Afhankelijk van de Linux-versie moet u mogelijk ook de volgende afhankelijkheden toevoegen:

* De gedeelde bibliotheken van de GNU C-bibliotheek (met inbegrip van de programmeer bibliotheek POSIX-threads, `libpthreads`)
* De OpenSSL-bibliotheek (`libssl.so.1.0.0` of `libssl.so.1.0.2`)
* De gedeelde bibliotheek voor ALSA-toepassingen (`libasound.so.2`)

Op Ubuntu moeten de GNU C-bibliotheken standaard al zijn geïnstalleerd. De laatste drie kunnen worden geïnstalleerd met behulp van de volgende opdrachten:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Op Debian 9 installeert u deze pakketten:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>Volgende stappen

* [Uw proefabonnement voor Speech ophalen](https://azure.microsoft.com/try/cognitive-services/)
* [Zie spraak herkennen inC#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
