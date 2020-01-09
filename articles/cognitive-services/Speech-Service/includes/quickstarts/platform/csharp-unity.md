---
title: 'Snelstartgids: Speech SDK voor C# Unity platform Setup-Speech Service'
titleSuffix: Azure Cognitive Services
description: Gebruik deze hand leiding om uw platform voor C# eenheid in te stellen met de Speech Service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/10/2019
ms.author: erhopf
ms.openlocfilehash: 8d9e05b5d8105cce8355d217cc6922c25bbe1730
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469286"
---
In deze hand leiding wordt beschreven hoe u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) voor [Unit](https://unity3d.com/)installeert.

> [!NOTE]
> De Speech SDK voor unit ondersteunt Windows Desktop (x86 en x64) of Universeel Windows-platform (x86, x64, ARM/ARM64), Android (x86, ARM32/64) en iOS (x64 Simulator, ARM32 en ARM64)

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

- [Unit 2018,3 of hoger](https://store.unity.com/) met [Unity 2019,1 voegt ondersteuning toe voor UWP ARM64](https://blogs.unity3d.com/2019/04/16/introducing-unity-2019-1/#universal).
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Versie 15,9 of hoger van Visual Studio 2017 is ook acceptabel.
- Voor ondersteuning voor Windows ARM64 installeert u de [optionele build tools voor ARM64 en de Windows 10 SDK voor ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

Voer de volgende stappen uit om de Speech SDK voor Unit te installeren:

1. Down load en open de [Speech SDK voor unit](https://aka.ms/csspeech/unitypackage), die is verpakt als een eenheids Asset-pakket (. unitypackage), en dat al is gekoppeld aan unit. Wanneer het Asset-pakket wordt geopend, wordt het dialoog venster **eenheids pakket importeren** weer gegeven. U moet mogelijk een leeg project maken en openen om deze stap te kunnen gebruiken.

   [het dialoog venster ![importeren van Unity package in de eenheids editor](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-unity-01-import.png#lightbox)

1. Zorg ervoor dat alle bestanden zijn geselecteerd en selecteer **importeren**. Na enkele ogen blikken wordt het eenheids Asset-pakket in uw project geïmporteerd.

Voor meer informatie over het importeren van Asset-pakketten in unit-eenheid raadpleegt u de [documentatie van Unity](https://docs.unity3d.com/Manual/AssetPackages.html).

U kunt nu door gaan naar de [volgende stappen](#next-steps) .

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list.md)]
