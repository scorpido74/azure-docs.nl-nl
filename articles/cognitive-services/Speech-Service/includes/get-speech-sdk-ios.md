---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 7ce193c2c2f5e10a27550da68a4c2d2fdcd1db7f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399979"
---
:::row:::
    :::column span="3":::
        Bij de ontwikkeling voor iOS zijn er twee Spraak-SDK's beschikbaar. De Objective-C Speech SDK is native beschikbaar als iOS CocoaPod-pakket. Als alternatief kan de .NET Speech SDK worden gebruikt met Xamarin.iOS terwijl deze .NET Standard 2.0 implementeert.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Zie <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Objective-C <span class="docon docon-navigate-external x-hidden-focus"> </span>importeren in Swift </a>voor meer informatie over het gebruik van de Objective-C Speech SDK met Swift.

### <a name="system-requirements"></a>Systeemvereisten

- Een macOS-versie 10.3 of hoger
- Target iOS 9.3 of hoger

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        Het iOS CocoaPod-pakket is beschikbaar voor download en gebruik met de <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (of hoger) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> geïntegreerde ontwikkelomgeving (IDE). Ten eerste, <a href="https://aka.ms/csspeech/iosbinary" target="_blank">download <span class="docon docon-navigate-external x-hidden-focus"> </span>de binaire CocoaPod </a>. Haal de pod in dezelfde map voor het beoogde gebruik, `pod` maak `target`een *Podfile* en lijst de as a .
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
        Xamarin.iOS onthult de volledige iOS SDK voor .NET-ontwikkelaars. Bouw volledig native iOS-apps met C# of F# in Visual Studio. Zie <a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin.iOS <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>voor meer informatie.
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

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS Speech SDK start snel Objective-C broncode<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">iOS Speech SDK start swift broncode snel<span class="docon docon-navigate-external x-hidden-focus"></span></a>