---
title: Unity-voorbeeld implementeren in Desktop
description: Snelstart die laat zien hoe u het Unity-voorbeeld kunt ophalen op een desktopcomputer
author: christophermanthei
ms.author: chmant
ms.date: 03/20/2020
ms.topic: quickstart
ms.openlocfilehash: dc566d2702fb24ef551e4900bed7b828d2597383
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/30/2020
ms.locfileid: "85557067"
---
# <a name="quickstart-deploy-unity-sample-to-desktop"></a>Quickstart: Unity-voorbeeld implementeren in Desktop

In deze snelstart wordt beschreven hoe u de voorbeeld-app voor Unity implementeert en uitvoert op een desktopcomputer.

In deze snelstart leert u het volgende:

> [!div class="checklist"]
>
>* De voorbeeld-app uit de snelstart bouwen voor een desktopcomputer
>* Het voorbeeld implementeren op een pc
>* Het voorbeeld uitvoeren op een pc

## <a name="prerequisites"></a>Vereisten

In deze snelstart wordt het voorbeeldproject geïmplementeerd vanuit [Snelstart: een model weergeven met Unity](render-model.md).

Controleer of uw referenties correct zijn opgeslagen bij de scène en u verbinding kunt maken met een sessie vanuit de Unity-editor.

## <a name="disable-virtual-reality-support"></a>Ondersteuning voor virtuele realiteit uitschakelen

Alleen platte bureaublad-apps worden momenteel op het bureaublad ondersteund, dus de ondersteuning voor VR moet worden uitgeschakeld.

1. Open *Bewerken > Projectinstellingen...*
1. Selecteer **Speler** aan de linkerkant.
1. Selecteer het tabblad **Instellingen Universeel Windows-platform**.
1. Vouw de **XR-instellingen** uit.
1. Schakel **Virtual Reality ondersteund** uit.\
    ![instellingen voor speler](./media/unity-disable-xr.png)
1. Vouw boven *XR-instellingen* de optie **Publicatie-instellingen** uit.
1. Zorg ervoor dat **Bureaublad** is ingeschakeld in **Ondersteunde apparaten**.

## <a name="build-the-sample-project"></a>Het voorbeeldproject bouwen

1. Open *Bestand -> Build-instellingen*.
1. Wijzig *Platform* in **Universeel Windows-platform** (**zelfstandige pc** wordt ook ondersteund, maar hier niet gebruikt, zie [platformbeperkingen](../reference/limits.md#platform-limitations)).
1. Stel *Doelapparaat* in op **Pc**.
1. Stel *Architectuur* in op **x86**.
1. Stel *Buildtype* in op **D3D-project**.\
  ![Build-instellingen](./media/unity-build-settings-pc.png)
1. Selecteer **Overschakelen naar platform**.
1. Wanneer u op **Build** drukt (of 'Bouwen en uitvoeren'), wordt u gevraagd om een map te selecteren waarin de oplossing moet worden opgeslagen.
1. Open de gegenereerde **Quickstart.sln** met Visual Studio.
1. Wijzig de configuratie in **Release** en **x86**.
1. Schakel de foutopsporingsmodus in op **Lokale machine**.\
  ![Configuratie van de oplossing](./media/unity-deploy-config-pc.png)
1. Bouw de oplossing (F7).

> [!WARNING]
> Zorg ervoor dat u **x86**selecteert. **UWP/x64** wordt momenteel niet ondersteund, zie [platformbeperkingen](../reference/limits.md#platform-limitations).

## <a name="launch-the-sample-project"></a>Het voorbeeldproject openen

Start de foutopsporing in Visual Studio (F5). De app wordt automatisch geïmplementeerd op de pc.

De voorbeeld-app moet worden gestart en vervolgens een nieuwe sessie starten. Na een tijdje is de sessie gereed en wordt het extern gerenderde model weergegeven.
Als u het voorbeeld een tweede keer wilt starten, kunt u dit ook vinden in het menu Start.

## <a name="next-steps"></a>Volgende stappen

In de volgende snelstart gaan we kijken naar het converteren van een aangepast model.

> [!div class="nextstepaction"]
> [Snelstart: een model converteren voor rendering](convert-model.md)
