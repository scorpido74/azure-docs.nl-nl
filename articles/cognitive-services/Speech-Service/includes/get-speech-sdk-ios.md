---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 7ce193c2c2f5e10a27550da68a4c2d2fdcd1db7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81399979"
---
:::row:::
    :::column span="3":::
        Bij het ontwikkelen voor iOS zijn er twee spraak-Sdk's beschikbaar. De SDK van de doel-C spraak is standaard beschikbaar als een iOS CocoaPod-pakket. De .NET Speech SDK kan ook worden gebruikt met Xamarin. iOS bij het implementeren van .NET Standard 2,0.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Zie voor meer informatie met behulp van de hand van de doel-C Speech SDK met SWIFT, <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">objectief-c importeren in SWIFT <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

### <a name="system-requirements"></a>Systeemvereisten

- Een macOS-versie 10,3 of hoger
- 9,3 of hoger van het doel van iOS

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        Het iOS CocoaPod-pakket is beschikbaar voor downloaden en gebruiken met de <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (of hoger <span class="docon docon-navigate-external x-hidden-focus"></span> )</a> Integrated Development Environment (IDE). <a href="https://aka.ms/csspeech/iosbinary" target="_blank">Down load eerst de binaire CocoaPod <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. Pak de pod in dezelfde map uit voor het beoogde gebruik, maak een *Podfile* en lijst de `pod` as a `target` .
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

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Xamarin. iOS maakt de volledige iOS SDK voor .NET-ontwikkel aars beschikbaar. Bouw volledig ingebouwde iOS-apps met behulp van C# of F # in Visual Studio. Zie <a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin. IOS <span class="docon docon-navigate-external x-hidden-focus"></span> </a>voor meer informatie.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp;❤️ &nbsp;        <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>Aanvullende bronnen

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS Speech SDK Quick Start-C-bron code <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">SWIFT-bron code voor iOS Speech SDK Quick Start <span class="docon docon-navigate-external x-hidden-focus"></span></a>