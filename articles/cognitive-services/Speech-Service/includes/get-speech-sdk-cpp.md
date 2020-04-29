---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5a06a0663601c221dd456b9cf4437cb9f32a18f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399940"
---
:::row:::
    :::column span="3":::
        De C++ Speech SDK is beschikbaar op Windows, Linux en macOS. Zie <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">micro soft. CognitiveServices. speech <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>(Engelstalig) voor meer informatie.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>C++ NuGet-pakket

De C++ Speech SDK kan worden geïnstalleerd via **Package Manager** met de volgende `Install-Package` opdracht.

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>Binaire bestanden voor C++ en header

U kunt ook de C++ Speech SDK installeren vanuit binaire bestanden. Down load de SDK als een <a href="https://aka.ms/csspeech/linuxbinary" target="_blank">. tar <span class="docon docon-navigate-external x-hidden-focus"></span> -pakket</a> en pak de bestanden uit in een door u gewenste map. De inhoud van dit pakket (met inbegrip van header bestanden voor x86-en x64-doel architecturen) is als volgt gestructureerd:

  | Pad                   | Beschrijving                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | Licentie                                              |
  | `ThirdPartyNotices.md` | Mededelingen van derden                                  |
  | `include`              | Header bestanden voor C++                                 |
  | `lib/x64`              | Systeem eigen x64-bibliotheek voor koppeling met uw toepassing |
  | `lib/x86`              | Systeem eigen x86-bibliotheek voor koppeling met uw toepassing |

  Als u een toepassing wilt maken, kopieert of verplaatst u de vereiste binaire bestanden (en bibliotheken) naar uw ontwikkel omgeving. Neem deze op als vereist in uw bouw proces.

#### <a name="additional-resources"></a>Aanvullende bronnen

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Quick Start-bron code voor Windows, Linux en macOS<span class="docon docon-navigate-external x-hidden-focus"></span></a>