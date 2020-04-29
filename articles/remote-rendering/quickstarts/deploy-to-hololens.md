---
title: Unity-voorbeeld implementeren in HoloLens
description: Quick start die laat zien hoe u het voor beeld van Unity kunt ophalen op de HoloLens
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: 75b6629ea924ec17888ec2f981ff85ed917434c9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679735"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Snelstartgids: Unit-voor beeld implementeren in HoloLens

In deze Quick Start wordt beschreven hoe u de Snelstartgids voor voor beeld kunt implementeren en uitvoeren voor Unit op een HoloLens 2.

In deze Quick Start leert u het volgende:

> [!div class="checklist"]
>
>* De Snelstartgids-voor beeld-app voor HoloLens bouwen
>* Het voor beeld implementeren op het apparaat
>* Het voor beeld uitvoeren op het apparaat

## <a name="prerequisites"></a>Vereisten

In deze Quick start gaan we het voorbeeld project implementeren van [Quick Start: een model met Unit renderen](render-model.md).

Zorg ervoor dat uw referenties correct zijn opgeslagen met de scène en dat u verbinding kunt maken met een sessie vanuit de Unity-editor.

## <a name="build-the-sample-project"></a>Het voorbeeld project bouwen

1. Open *File > build Settings*.
1. *Platform* wijzigen in **universeel Windows-platform**
1. *Doel apparaat* instellen op **HoloLens**
1. *Architectuur* instellen op **ARM64**
1. *Build-type* instellen op **D3D project** ![build-instellingen](./media/unity-build-settings.png)
1. Selecteer **overschakelen naar platform**
1. Wanneer u op **Build** (of ' bouwen en uitvoeren ' drukt), wordt u gevraagd een map te selecteren waarin de oplossing moet worden opgeslagen
1. De gegenereerde **Snelstartgids. SLN** openen met Visual Studio
1. Wijzig de configuratie in **release** en **ARM64**
1. De foutopsporingsmodus overschakelen naar de configuratie van de oplossing voor **externe computers** ![](media/unity-deploy-config.png)
1. De oplossing bouwen (F7)
1. Ga voor het project Quick Start naar *eigenschappen > fout opsporing*
    1. Controleer of de configuratie *versie* actief is
    1. *Fout opsporing instellen om* naar een **externe computer** te starten
    1. Wijzig de *computer naam* in het **IP-adres van uw HoleLens**

## <a name="launch-the-sample-project"></a>Het voorbeeld project starten

1. Sluit de HoloLens aan op uw PC met een USB-kabel.
1. Start het fout opsporingsprogramma in Visual Studio (F5). De app wordt automatisch geïmplementeerd op het apparaat.

De voor beeld-app moet worden gestart en vervolgens een nieuwe sessie starten. Na een tijdje is de sessie gereed en wordt het extern gerenderde model vóór u weer gegeven.
Als u het voor beeld een tweede keer wilt starten, kunt u dit ook vinden in het menu Start van HoloLens.

## <a name="next-steps"></a>Volgende stappen

In de volgende Snelstartgids gaan we kijken naar het converteren van een aangepast model.

> [!div class="nextstepaction"]
> [Snelstartgids: een model voor rendering converteren](convert-model.md)
