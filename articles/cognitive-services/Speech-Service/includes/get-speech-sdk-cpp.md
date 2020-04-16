---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5a06a0663601c221dd456b9cf4437cb9f32a18f8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399940"
---
:::row:::
    :::column span="3":::
        De C++ Speech SDK is beschikbaar op Windows, Linux en macOS. Zie <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft.CognitiveServices.Speech <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>voor meer informatie.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>C++ NuGet-pakket

De C++ Speech SDK kan **Package Manager** met de `Install-Package` volgende opdracht vanuit Package Manager worden geïnstalleerd.

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>C++ binaire bestanden en koptekstbestanden

Als alternatief kan de C++ Speech SDK worden geïnstalleerd vanuit binaire bestanden. Download de SDK als <a href="https://aka.ms/csspeech/linuxbinary" target="_blank">een .tar-pakket <span class="docon docon-navigate-external x-hidden-focus"></span> </a> en pak de bestanden uit in een map naar keuze. De inhoud van dit pakket (inclusief headerbestanden voor zowel x86- als x64-doelarchitecturen) is als volgt gestructureerd:

  | Pad                   | Beschrijving                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | Licentie                                              |
  | `ThirdPartyNotices.md` | Mededelingen van derden                                  |
  | `include`              | Koptekstbestanden voor C++                                 |
  | `lib/x64`              | Native x64-bibliotheek voor koppeling met uw toepassing |
  | `lib/x86`              | Native x86-bibliotheek voor koppeling met uw toepassing |

  Als u een toepassing wilt maken, kopieert of verplaatst u de vereiste binaire bestanden (en bibliotheken) naar uw ontwikkelomgeving. Neem ze op zoals vereist in uw bouwproces.

#### <a name="additional-resources"></a>Aanvullende bronnen

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Windows, Linux en macOS quickstart C++ broncode<span class="docon docon-navigate-external x-hidden-focus"></span></a>