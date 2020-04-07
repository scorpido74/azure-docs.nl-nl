---
title: Holografische remoting en externe weergave in eenheid gebruiken
description: Hoe Holographic Remoting preview kan worden gebruikt in combinatie met Azure Remote Rendering
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: ac47a2922e92233f0acabf75817a712671306bc1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681205"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Holografische remoting en externe weergave in eenheid gebruiken

[Holografische Remoting](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) en Azure Remote Rendering sluiten elkaar uit binnen één toepassing. Als zodanig is [de Unity-speelmodus](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) ook niet beschikbaar.

Voor elke looppas van de redacteur van de Eenheid slechts kan één van twee worden gebruikt. Als u de andere wilt gebruiken, start u Unity eerst opnieuw op.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>De afspeelmodus van Unity gebruiken om een voorbeeld te geven van Hololens 2

 De eenheidsspelmodus kan nog steeds worden gebruikt, bijvoorbeeld om de gebruikersinterface van de toepassing te testen. Het is echter van vitaal belang dat ARR nooit wordt geïnitialiseerd. Anders zal het crashen.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Een WMR VR-headset gebruiken om een voorbeeld te bekijken op het bureaublad

Als er een Windows Mixed Reality VR-headset aanwezig is, kan deze worden gebruikt om een voorbeeld te bekijken in Unity. In dit geval is het prima om ARR te initialiseren, maar het zal niet mogelijk zijn om verbinding te maken met een sessie terwijl de WMR-headset wordt gebruikt.
