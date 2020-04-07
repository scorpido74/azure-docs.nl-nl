---
title: Een model renderen met Unity
description: Snelstart die de gebruiker door de stappen leidt om een model weer te geven
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: b0af45ba4a6b1ca7f9e751af082ff0db80776ec0
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679784"
---
# <a name="quickstart-render-a-model-with-unity"></a>Snelstart: een model weergeven met Unity

Deze quickstart heeft betrekking op het uitvoeren van een Unity-voorbeeld waarmee een ingebouwd model op afstand wordt weergegeven met behulp van de ARR-service (Azure Remote Rendering).

We zullen niet in detail treden over de ARR API zelf of hoe we een nieuw Unity-project kunnen opzetten. Deze onderwerpen komen aan bod in [Tutorial: Het opzetten van een Unity-project vanaf nul.](../tutorials/unity/project-setup.md)

In deze quickstart leer je hoe je:
> [!div class="checklist"]
>
>* Uw lokale ontwikkelingsomgeving instellen
>* Download en bouw de ARR Quickstart voorbeeld-app voor Unity
>* Een model weergeven in de ARR Quickstart-voorbeeld-app

## <a name="prerequisites"></a>Vereisten

Als u toegang wilt krijgen tot de Azure Remote Rendering-service, moet u eerst [een account maken.](../how-tos/create-an-account.md)

De volgende software moet worden geïnstalleerd:

* Windows SDK 10.0.18362.0 [(downloaden)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* De nieuwste versie van Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(download)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(download)](https://unity3d.com/get-unity/download)
  * Installeer deze modules in Unity:
    * **UWP** - Ondersteuning voor universal Windows Platform Build
    * **IL2CPP** - Windows Build Support (IL2CPP)

## <a name="clone-the-sample-app"></a>De voorbeeld-app klonen

Open een opdrachtprompt `cmd` (typ in het startmenu van Windows) en wijzig naar een map waar u het ARR-voorbeeldproject wilt opslaan.

Voer de volgende opdrachten uit:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

Met de laatste opdracht wordt een submap gemaakt in de ARR-map met de verschillende voorbeeldprojecten voor Azure Remote Rendering.

De quickstart voorbeeld-app voor Unity is te vinden in de submap *Unity/Quickstart.*

## <a name="rendering-a-model-with-the-unity-sample-project"></a>Een model renderen met het voorbeeldproject Unity

Open de Unity Hub en voeg het voorbeeldproject toe, de map *ARR\azure-remote-rendering\Unity\Quickstart.*
Open het project. Laat Unity indien nodig het project upgraden naar uw geïnstalleerde versie.

Het standaardmodel dat we renderen is een [ingebouwd voorbeeldmodel.](../samples/sample-model.md) We laten zien hoe je een aangepast model converteert met de ARR-conversieservice in de [volgende quickstart.](convert-model.md)

### <a name="enter-your-account-info"></a>Voer uw accountgegevens in

1. Navigeer in de eenheidsassetbrowser naar de map *Scènes* en open de **quickstartscène.**
1. Selecteer *in*de hiërarchie het spelobject **RemoteRendering.**
1. Voer *in*de inspecteur uw [accountgegevens](../how-tos/create-an-account.md)in.

![ARR-accountgegevens](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> Azure Portal geeft het domein van uw account alleen weer als *mixedreality.azure.com.* Dit is onvoldoende voor het succesvol aansluiten.
> **Stel AccountDomain** in op `<region>.mixedreality.azure.com`, waar `<region>` is een van de beschikbare [regio's bij u in de buurt.](../reference/regions.md)

Later willen we dit project implementeren op een HoloLens en verbinding maken met de Remote Rendering-service vanaf dat apparaat. Aangezien we geen gemakkelijke manier hebben om de referenties op het apparaat in te voeren, slaat het voorbeeld van QuickStart **de referenties op in de Unity-scène.**

> [!WARNING]
> Zorg ervoor dat u het project niet controleren met uw opgeslagen referenties in een opslagplaats waar het geheime inloggegevens zou lekken!

### <a name="create-a-session-and-view-the-default-model"></a>Een sessie maken en het standaardmodel weergeven

Druk op de **knop Afspelen** van Unity om de sessie te starten. U ziet een overlay met statustekst, onder aan de viewport in het deelvenster *Game.* De sessie zal een reeks van staat overgangen ondergaan. In de **status Start** wordt de externe VM gesponnen, wat enkele minuten duurt. Na succes, het overgangen naar de **Ready** staat. Nu gaat de sessie de **status Verbinden** in, waar de renderingruntime op die VM wordt bereikt. Wanneer het voorbeeld is geslaagd, wordt deze overgezet naar de **status Verbonden.** Op dit punt zal het beginnen met het downloaden van het model voor rendering. Vanwege de grootte van het model kan de download nog een paar minuten duren. Dan verschijnt het op afstand gerenderde model.

![Uitvoer uit het monster](media/arr-sample-output.png)

Gefeliciteerd! U bekijkt nu een op afstand weergegeven model!

## <a name="inspecting-the-scene"></a>De scène inspecteren

Zodra de externe renderingverbinding is uitgevoerd, wordt het deelvenster Controle bijgewerkt met aanvullende statusgegevens:

![Unity sample afspelen](./media/arr-sample-configure-session-running.png)

U nu de scènegrafiek verkennen door het nieuwe knooppunt te selecteren en **op Kinderen weergeven** in de inspecteur te klikken.

![Eenheidshiërarchie](./media/unity-hierarchy.png)

Er is een [gesneden vlak](../overview/features/cut-planes.md) object in de scène. Probeer het in te schakelen in zijn eigenschappen en verplaats het rond:

![Het veranderen van het gesneden vlak](media/arr-sample-unity-cutplane.png)

Als u transformaties wilt synchroniseren, klikt u nu op **Synchroniseren** of schakelt u de optie **Elk frame synchroniseren in.** Voor componenteigenschappen is alleen het wijzigen ervan voldoende.

## <a name="next-steps"></a>Volgende stappen

In de volgende quickstart zullen we het monster implementeren in een HoloLens om het op afstand gerenderde model in zijn oorspronkelijke grootte te bekijken.

> [!div class="nextstepaction"]
> [Snelstart: Unity-voorbeeld implementeren in HoloLens](deploy-to-hololens.md)

Als alternatief kan het voorbeeld ook worden geïmplementeerd op een desktop-pc.

> [!div class="nextstepaction"]
> [Snelstart: voorbeeld van Eenheid implementeren op bureaublad](deploy-to-desktop.md)