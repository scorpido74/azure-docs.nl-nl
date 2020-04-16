---
title: Unity-voorbeeld implementeren in Desktop
description: Quickstart die laat zien hoe u het Unity-voorbeeld op een desktop-pc krijgen
author: christophermanthei
ms.author: chmant
ms.date: 03/20/2020
ms.topic: quickstart
ms.openlocfilehash: 95993a54d321c9581d35f12b56d60e4ea052e505
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415723"
---
# <a name="quickstart-deploy-unity-sample-to-desktop"></a>Snelstart: voorbeeld van Eenheid implementeren op bureaublad

Deze quickstart heeft betrekking op het implementeren en uitvoeren van de quickstart voorbeeld-app voor Unity naar een desktop-pc.

In deze quickstart leer je hoe je:

> [!div class="checklist"]
>
>* De quickstart voorbeeld-app voor bureaublad bouwen
>* Het voorbeeld implementeren op een pc
>* Het voorbeeld op een pc uitvoeren

## <a name="prerequisites"></a>Vereisten

In deze quickstart implementeren we het voorbeeldproject van [Quickstart: Render een model met Unity](render-model.md).

Zorg ervoor dat uw referenties goed worden opgeslagen met de scène en u verbinding maken met een sessie vanuit de Unity-editor.

## <a name="disable-virtual-reality-support"></a>Ondersteuning voor virtual reality uitschakelen

Alleen platte desktop-apps worden momenteel ondersteund op desktop, dus VR-ondersteuning moet worden uitgeschakeld.

1. Open *> Projectinstellingen bewerken...*
1. Selecteer **Player** aan de linkerkant.
1. Selecteer het tabblad **Instellingen voor universele Windows-platforms.**
1. De **XR-instellingen**uitbreiden.
1. **Virtual Reality Ondersteunde**uitschakelen .
    ![spelersinstellingen](./media/unity-disable-xr.png)
1. Boven *XR-instellingen,* **uitvouwen Publicatie-instellingen**.
1. Controleer in **Ondersteunde apparaatfamilies**of **het bureaublad** is ingeschakeld.

## <a name="build-the-sample-project"></a>Het voorbeeldproject bouwen

1. Open *>-buildinstellingen*.
1. *Platform* wijzigen in **het Universele Windows-platform**.
1. *Doelapparaat* instellen op **pc**.
1. *Architectuur* instellen op **x86**.
1. *Buildtype* instellen op **D3D-project**.
  ![Build-instellingen](./media/unity-build-settings-pc.png)
1. Selecteer **Overschakelen naar platform**.
1. Wanneer u op **Build** (of 'Build And Run' drukt), wordt u gevraagd een map te selecteren waarin de oplossing moet worden opgeslagen.
1. Open de gegenereerde **Quickstart.sln** met Visual Studio.
1. Wijzig de configuratie in **Release** en **x86**.
1. Schakel de foutopsporingsmodus over op **Lokale machine**.
  ![Oplossingsconfiguratie](./media/unity-deploy-config-pc.png)
1. Bouw de oplossing (F7).

> [!WARNING]
> Selecteer **x86**. **x64** wordt momenteel niet ondersteund, zie [platformbeperkingen](../reference/limits.md#platform-limitations).

## <a name="launch-the-sample-project"></a>Het voorbeeldproject starten

Start de foutopsporing in Visual Studio (F5). De app wordt automatisch geïmplementeerd op de pc.

De voorbeeld-app moet starten en vervolgens een nieuwe sessie starten. Na een tijdje is de sessie klaar en verschijnt het op afstand gerenderde model voor u.
Als u het voorbeeld later nog een tweede keer wilt starten, u het nu ook vinden in het menu Start.

## <a name="next-steps"></a>Volgende stappen

In de volgende quickstart zullen we een kijkje nemen bij het converteren van een aangepast model.

> [!div class="nextstepaction"]
> [Snelstart: een model converteren voor rendering](convert-model.md)
