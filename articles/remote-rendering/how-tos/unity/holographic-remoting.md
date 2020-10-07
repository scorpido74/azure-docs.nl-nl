---
title: Holographic Remoting en Remote Rendering gebruiken in Unity
description: Hoe Holographic Remoting preview kan worden gebruikt in combi natie met Azure remote rendering
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: 180af30f57a8123b6e90cc8b11848b92b3c86db1
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802171"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Holographic Remoting en Remote Rendering gebruiken in Unity

[Holographic Remoting](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) en de externe rendering van Azure sluiten elkaar wederzijds uit binnen één toepassing. Als zodanig is [Unit Play-modus](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) ook niet beschikbaar.

Voor elke uitvoering van Unity editor kan slechts een van de twee worden gebruikt. Als u het andere item wilt gebruiken, moet u eerst de eenheid opnieuw opstarten.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Eenheids Play modus gebruiken om voor beeld op HoloLens 2 te bekijken

 Unit Play-modus kan nog steeds worden gebruikt, bijvoorbeeld om de gebruikers interface van de toepassing te testen. Het is echter essentieel dat ARR nooit wordt geïnitialiseerd. Anders wordt het vastlopen.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Een WMR VR-headset gebruiken om op bureau blad te bekijken

Als er een Windows Mixed Reality VR-headset aanwezig is, kan deze worden gebruikt om een voor beeld van de binnen eenheid te bekijken. In dit geval is het goed om ARR te initialiseren, maar het is niet mogelijk om verbinding te maken met een sessie terwijl de headset WMR wordt gebruikt.
