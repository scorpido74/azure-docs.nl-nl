---
title: Een model weergeven met Unity
description: Quick Start waarmee de gebruiker wordt begeleid door de stappen voor het renderen van een model
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: b0af45ba4a6b1ca7f9e751af082ff0db80776ec0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679784"
---
# <a name="quickstart-render-a-model-with-unity"></a>Quick Start: een model met Unit renderen

In deze Snelstartgids wordt beschreven hoe u een voor beeld van een eenheid uitvoert waarmee een ingebouwd model op afstand wordt weer gegeven met de Azure remote rendering-service (ARR).

We gaan niet verder met de ARR API zelf of het instellen van een nieuw Unity-project. Deze onderwerpen worden behandeld in de [zelf studie: een geheel nieuw eenheids project instellen](../tutorials/unity/project-setup.md).

In deze Quick Start leert u het volgende:
> [!div class="checklist"]
>
>* Uw lokale ontwikkel omgeving instellen
>* De voor beeld-app voor ARR-Snelstartgids ophalen en bouwen voor Unity
>* Een model weer geven in de voor beeld-app ARR Quick Start

## <a name="prerequisites"></a>Vereisten

Als u toegang wilt krijgen tot de Azure remote rendering-service, moet u eerst [een account maken](../how-tos/create-an-account.md).

De volgende software moet zijn geïnstalleerd:

* Windows SDK 10.0.18362.0 [(down load)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* De nieuwste versie van Visual Studio 2019 [(down load)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(down load)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(down load)](https://unity3d.com/get-unity/download)
  * Deze modules installeren in Unity:
    * Ondersteuning voor **UWP** -universeel Windows-platform-build
    * **IL2CPP** -ondersteuning voor Windows-Build (IL2CPP)

## <a name="clone-the-sample-app"></a>De voorbeeld-app klonen

Open een opdracht prompt (Typ `cmd` in het menu Start van Windows) en ga naar een map waarin u het ARR-voorbeeld project wilt opslaan.

Voer de volgende opdrachten uit:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

Met de laatste opdracht maakt u een submap in de map ARR met de verschillende voorbeeld projecten voor de externe rendering van Azure.

De Quick start-voor beeld-app voor Unity is gevonden in de submap *Unit/Quick*start.

## <a name="rendering-a-model-with-the-unity-sample-project"></a>Een model samen stellen met het unit-voorbeeld project

Open de eenheid voor Unity en voeg het voorbeeld project toe. Dit is de map *ARR\azure-Remote-rendering\Unity\Quickstart* .
Open het project. Indien nodig, mag eenheid het project upgraden naar de geïnstalleerde versie.

Het standaard model dat wordt weer gegeven, is een [ingebouwd voorbeeld model](../samples/sample-model.md). In de [volgende Snelstartgids](convert-model.md)wordt uitgelegd hoe u een aangepast model converteert met de ARR-conversie service.

### <a name="enter-your-account-info"></a>Voer uw account gegevens in

1. Ga in de activa van Unity Asset naar de map *scènes* en open de **Quick** start-scène.
1. Selecteer in de *hiërarchie*het **RemoteRendering** -spel object.
1. Voer in de *Inspector*uw [account referenties](../how-tos/create-an-account.md)in.

![ARR-account gegevens](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> In azure portal wordt het domein van uw account alleen als *mixedreality.Azure.com*weer gegeven. Dit is onvoldoende voor een geslaagde verbinding.
> Stel **AccountDomain** in `<region>.mixedreality.azure.com`op, `<region>` waarbij [een van de beschik bare regio's bij u in de buurt](../reference/regions.md)is.

Later willen we dit project implementeren op een HoloLens en verbinding maken met de externe rendering-service vanaf dat apparaat. Omdat we geen eenvoudige manier hebben om de referenties op het apparaat in te voeren, slaat het Quick start-voor beeld **de referenties op in de eenheids scène**.

> [!WARNING]
> Zorg ervoor dat u het project niet controleert met uw opgeslagen referenties in een bepaalde opslag plaats waar de aanmeldings gegevens voor het geheim worden gelekt.

### <a name="create-a-session-and-view-the-default-model"></a>Een sessie maken en het standaard model weer geven

Druk op de knop **afspelen** om de sessie te starten. U ziet een overlay met de status tekst onder aan de View Port in het deel venster *Game* . De sessie zal een reeks status overgangen ondergaan. De externe virtuele machine wordt **gestart** , wat enkele minuten in beslag neemt. Wanneer de bewerking is voltooid, wordt de status **gereed** . Nu voert de sessie de **verbindings** status in, waarbij wordt geprobeerd de rendering-runtime op die VM te bereiken. Wanneer dit is gelukt, wordt de status van het voor beeld overgezet naar de **verbinding** . Op dit moment wordt het model voor rendering gestart. De down load kan een paar minuten duren vanwege de grootte van het model. Vervolgens wordt het extern gerenderde model weer gegeven.

![Uitvoer van het voor beeld](media/arr-sample-output.png)

Gefeliciteerd! U bekijkt nu een extern samengesteld model.

## <a name="inspecting-the-scene"></a>De scène controleren

Zodra de verbinding voor externe rendering actief is, wordt het deel venster Inspector bijgewerkt met aanvullende status informatie:

![Eenheids voorbeeld afspelen](./media/arr-sample-configure-session-running.png)

U kunt de scène grafiek nu verkennen door het nieuwe knoop punt te selecteren en te klikken op **onderliggende items weer geven** in de Inspector.

![Unity-hiërarchie](./media/unity-hierarchy.png)

De scène bevat een [knip vlak](../overview/features/cut-planes.md) object. Probeer het in de eigenschappen in te scha kelen en te verplaatsen:

![Het Knip vlak wijzigen](media/arr-sample-unity-cutplane.png)

Als u trans formaties wilt synchroniseren, klikt u op **Nu synchroniseren** of schakelt u de optie **elke frame synchroniseren** in. Voor onderdeel eigenschappen is het voldoende dat u deze hoeft te wijzigen.

## <a name="next-steps"></a>Volgende stappen

In de volgende Snelstartgids implementeren we het voor beeld naar een HoloLens om het extern gerenderde model in de oorspronkelijke grootte weer te geven.

> [!div class="nextstepaction"]
> [Snelstartgids: Unit-voor beeld implementeren in HoloLens](deploy-to-hololens.md)

Het voor beeld kan ook worden geïmplementeerd op een desktop-PC.

> [!div class="nextstepaction"]
> [Snelstartgids: Unit-voor beeld implementeren op Desktop](deploy-to-desktop.md)