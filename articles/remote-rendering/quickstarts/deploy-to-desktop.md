---
title: Unity-voorbeeld implementeren in Desktop
description: Quick start die laat zien hoe u het unit-voor beeld kunt ophalen op een desktop computer
author: christophermanthei
ms.author: chmant
ms.date: 03/20/2020
ms.topic: quickstart
ms.openlocfilehash: 95993a54d321c9581d35f12b56d60e4ea052e505
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81415723"
---
# <a name="quickstart-deploy-unity-sample-to-desktop"></a>Snelstartgids: Unit-voor beeld implementeren op Desktop

In deze Quick Start wordt beschreven hoe u de voor beeld-app voor het Snelstartgids voor unit implementeert en uitvoert op een desktop-PC.

In deze Quick Start leert u het volgende:

> [!div class="checklist"]
>
>* De Quick start-voor beeld-app bouwen voor desktop
>* Het voor beeld implementeren op een PC
>* Het voor beeld uitvoeren op een PC

## <a name="prerequisites"></a>Vereisten

In deze Quick start gaan we het voorbeeld project implementeren van [Quick Start: een model met Unit renderen](render-model.md).

Zorg ervoor dat uw referenties correct zijn opgeslagen met de scène en dat u verbinding kunt maken met een sessie vanuit de Unity-editor.

## <a name="disable-virtual-reality-support"></a>Ondersteuning voor Virtual Reality uitschakelen

Alleen platte bureau blad-apps worden momenteel op het bureau blad ondersteund, dus de ondersteuning voor VR moet worden uitgeschakeld.

1. *> project instellingen bewerken...*
1. Selecteer **speler** aan de linkerkant.
1. Selecteer het tabblad **universeel Windows-platform instellingen** .
1. Vouw de **XR-instellingen**uit.
1. Schakel **Virtual Reality**uit die wordt ondersteund.
    ![instellingen voor speler](./media/unity-disable-xr.png)
1. Klik boven *XR-instellingen*op publicatie- **instellingen**.
1. Zorg ervoor dat het selectie vakje op het **bureau blad** van **ondersteunde apparaten**wordt ingeschakeld.

## <a name="build-the-sample-project"></a>Het voorbeeld project bouwen

1. Open *File > build Settings*.
1. Wijzig het *platform* in **universeel Windows-platform**.
1. Stel *doel apparaat* in op **PC**.
1. Stel de *architectuur* in op **x86**.
1. Stel het *type Build* in op het **D3D-project**.
  ![Instellingen bouwen](./media/unity-build-settings-pc.png)
1. Selecteer **overschakelen naar platform**.
1. Wanneer u op **Build** (of ' bouwen en uitvoeren ' drukt), wordt u gevraagd om een map te selecteren waarin de oplossing moet worden opgeslagen.
1. Open de gegenereerde **Snelstartgids. SLN** met Visual Studio.
1. Wijzig de configuratie in **release** en **x86**.
1. Schakel de foutopsporingsmodus naar de **lokale computer**.
  ![Oplossings configuratie](./media/unity-deploy-config-pc.png)
1. Bouw de oplossing (F7).

> [!WARNING]
> Zorg ervoor dat u **x86**selecteert. **x64** wordt momenteel niet ondersteund. Zie [beperkingen](../reference/limits.md#platform-limitations)voor het platform.

## <a name="launch-the-sample-project"></a>Het voorbeeld project starten

Start het fout opsporingsprogramma in Visual Studio (F5). De app wordt automatisch geïmplementeerd op de PC.

De voor beeld-app moet worden gestart en vervolgens een nieuwe sessie starten. Na een tijdje is de sessie gereed en wordt het extern gerenderde model vóór u weer gegeven.
Als u het voor beeld een tweede keer wilt starten, kunt u dit ook vinden in het menu Start.

## <a name="next-steps"></a>Volgende stappen

In de volgende Snelstartgids gaan we kijken naar het converteren van een aangepast model.

> [!div class="nextstepaction"]
> [Snelstartgids: een model voor rendering converteren](convert-model.md)
