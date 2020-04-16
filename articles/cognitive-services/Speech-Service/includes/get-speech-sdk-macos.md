---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 57de7f1e7c37fec66cda666d3f144e52849a026f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400010"
---
Bij de ontwikkeling voor macOS zijn er drie Spraak-SDK's beschikbaar.

- De Objective-C Speech SDK is native verkrijgbaar als CocoaPod-pakket
- De .NET Speech SDK kan worden gebruikt met **Xamarin.Mac** als het implementeert .NET Standard 2.0
- De Python Speech SDK is beschikbaar als PyPI-module

> [!TIP]
> Zie <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Objective-C <span class="docon docon-navigate-external x-hidden-focus"> </span>importeren in Swift </a>voor meer informatie over het gebruik van de Objective-C Speech SDK met Swift.

### <a name="system-requirements"></a>Systeemvereisten

- Een macOS-versie 10.13 of hoger

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        Het macOS CocoaPod-pakket is beschikbaar voor download en gebruik met de <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (of hoger) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> geïntegreerde ontwikkelomgeving (IDE). Ten eerste, <a href="https://aka.ms/csspeech/macosbinary" target="_blank">download <span class="docon docon-navigate-external x-hidden-focus"> </span>de binaire CocoaPod </a>. Haal de pod in dezelfde map voor het beoogde gebruik, `pod` maak `target`een *Podfile* en lijst de as a .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.11.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac onthult de volledige macOS SDK voor .NET-ontwikkelaars om native Mac-toepassingen te bouwen met Behulp van C#. Zie <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin.Mac <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>voor meer informatie.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>Aanvullende bronnen

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS Speech SDK start snel Objective-C broncode<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS Speech SDK snel start Swift broncode<span class="docon docon-navigate-external x-hidden-focus"></span></a>