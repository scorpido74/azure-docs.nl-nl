---
title: Unity-voorbeeld implementeren op HoloLens
description: Quickstart die laat zien hoe je het Unity-monster op de HoloLens krijgt
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: 75b6629ea924ec17888ec2f981ff85ed917434c9
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679735"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Snelstart: Unity-voorbeeld implementeren in HoloLens

Deze quickstart heeft betrekking op het implementeren en uitvoeren van de quickstart sample app voor Unity naar een HoloLens 2.

In deze quickstart leer je hoe je:

> [!div class="checklist"]
>
>* De quickstart-voorbeeld-app voor HoloLens bouwen
>* Het voorbeeld op het apparaat implementeren
>* Het monster op het apparaat uitvoeren

## <a name="prerequisites"></a>Vereisten

In deze quickstart implementeren we het voorbeeldproject van [Quickstart: Render een model met Unity](render-model.md).

Zorg ervoor dat uw referenties goed worden opgeslagen met de scène en u verbinding maken met een sessie vanuit de Unity-editor.

## <a name="build-the-sample-project"></a>Het voorbeeldproject bouwen

1. Open *>-buildinstellingen*.
1. *Platform* wijzigen in **universeel Windows-platform**
1. *Doelapparaat* instellen op **HoloLens**
1. *Architectuur instellen* op **ARM64**
1. *Buildtype* instellen op **D3D Project** ![Build-instellingen](./media/unity-build-settings.png)
1. Switch **naar platform** selecteren
1. Wanneer u op **Build** (of 'Build And Run' drukt), wordt u gevraagd een map te selecteren waarin de oplossing moet worden opgeslagen
1. Open de gegenereerde **Quickstart.sln** met Visual Studio
1. De configuratie wijzigen in **Release** en **ARM64**
1. De foutopsporingsmodus overschakelen naar de configuratie **van externe machineoplossing** ![](media/unity-deploy-config.png)
1. Bouw de oplossing (F7)
1. Ga voor het project 'Quickstart' naar *Eigenschappen > Foutopsporing*
    1. Controleer of de configuratie *Release* actief is
    1. *Foutopsporing instellen om te starten op* externe **machine**
    1. *Machinenaam wijzigen* in het **IP van uw HoleLens**

## <a name="launch-the-sample-project"></a>Het voorbeeldproject starten

1. Sluit de HoloLens aan met een USB-kabel op uw pc.
1. Start de foutopsporing in Visual Studio (F5). Het zal automatisch de app implementeren op het apparaat.

De voorbeeld-app moet starten en vervolgens een nieuwe sessie starten. Na een tijdje is de sessie klaar en verschijnt het op afstand gerenderde model voor u.
Als je het voorbeeld later nog een tweede keer wilt starten, kun je het nu ook vinden in het Startmenu van De HoloLens.

## <a name="next-steps"></a>Volgende stappen

In de volgende quickstart zullen we een kijkje nemen bij het converteren van een aangepast model.

> [!div class="nextstepaction"]
> [Snelstart: een model converteren voor rendering](convert-model.md)
