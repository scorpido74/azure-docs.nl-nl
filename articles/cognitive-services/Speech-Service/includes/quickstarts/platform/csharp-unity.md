---
title: 'Snelstart: Speech SDK voor C# Unity-platforminstellingen - Spraakservice'
titleSuffix: Azure Cognitive Services
description: Gebruik deze handleiding om uw platform in te stellen voor C# Unity met de Spraakservice SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: 8d9e05b5d8105cce8355d217cc6922c25bbe1730
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75469286"
---
Deze handleiding laat zien hoe u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) voor [Unity installeert.](https://unity3d.com/)

> [!NOTE]
> De SpraakSDK voor Unity ondersteunt Windows Desktop (x86 en x64) of Universal Windows Platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) en iOS (x64 simulator, ARM32 en ARM64)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

- [Unity 2018.3 of hoger](https://store.unity.com/) met [Unity 2019.1 die ondersteuning toevoegt voor UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Versie 15.9 of hoger van Visual Studio 2017 is ook acceptabel.
- Installeer voor ondersteuning voor Windows ARM64 de [optionele buildtools voor ARM64 en de Windows 10 SDK voor ARM64.](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/)

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voer de volgende stappen uit om de SpraakSDK voor Unity te installeren:

1. Download en open de [Speech SDK for Unity](https://aka.ms/csspeech/unitypackage), die is verpakt als een Unity asset package (.unitypackage), en moet al worden geassocieerd met Unity. Wanneer het assetpakket wordt geopend, wordt het dialoogvenster **Eenheidspakket importeren** weergegeven. Mogelijk moet u een leeg project maken en openen om deze stap te laten werken.

   [![Dialoogvenster Eenheidspakket importeren in de Eenheidseditor](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Controleer of alle bestanden zijn geselecteerd en selecteer **Importeren**. Na enkele ogenblikken wordt het Unity asset pakket geïmporteerd in uw project.

Zie de [unity-documentatie](https://docs.unity3d.com/Manual/AssetPackages.html)voor meer informatie over het importeren van assetpakketten in Unity.

U nu verder gaan naar [Volgende stappen](#next-steps) hieronder.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list.md)]
