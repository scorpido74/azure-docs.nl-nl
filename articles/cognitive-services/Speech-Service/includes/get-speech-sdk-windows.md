---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 138a9ef9d483ca0d460bab7185d646669650f83e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399883"
---
:::row:::
    :::column span="3":::
        De SpraakSDK ondersteunt Windows 10- en Windows Server 2016- of latere versies. Eerdere versies worden **niet** officieel ondersteund. Het is mogelijk om delen van de Speech SDK te gebruiken met eerdere versies van Windows, hoewel het niet wordt aangeraden.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>Systeemvereisten

De Speech SDK op Windows vereist de <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Microsoft Visual C++ <span class="docon docon-navigate-external x-hidden-focus"></span> Redistributable voor Visual Studio 2019</a> op het systeem.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">Installeren voor x86<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">Installeren voor x64<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">Installeren voor ARMx64<span class="docon docon-navigate-external x-hidden-focus"></span></a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

Voor microfooninvoer moeten de bibliotheken van de Media Foundation worden geïnstalleerd. Deze bibliotheken maken deel uit van Windows 10 en Windows Server 2016. Het is mogelijk om de Speech SDK te gebruiken zonder deze bibliotheken, zolang een microfoon niet wordt gebruikt als het audio-invoerapparaat.

De vereiste Speech SDK-bestanden kunnen worden geïmplementeerd in dezelfde map als uw toepassing. Op deze manier heeft uw toepassing rechtstreeks toegang tot de bibliotheken. Zorg ervoor dat u de juiste versie (x86/x64) selecteert die overeenkomt met uw toepassing.

| Naam                                            | Functie                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core SDK, vereist voor native en beheerde implementatie |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Vereist voor beheerde implementatie                      |

> [!NOTE]
> Te beginnen met de release 1.3.0 het bestand `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (verzonden in eerdere releases) is niet meer nodig. De functionaliteit is nu geïntegreerd in de core SDK.

> [!IMPORTANT]
> Controleer bij het Project Windows Forms App (.NET Framework) C# of de bibliotheken zijn opgenomen in de implementatie-instellingen van uw project. U dit `Properties -> Publish Section`hieronder controleren. Klik `Application Files` op de knop en zoek overeenkomstige bibliotheken in de lijst scroll down. Controleer of de waarde `Included`is ingesteld op . Visual Studio bevat het bestand wanneer het project wordt gepubliceerd/geïmplementeerd.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
