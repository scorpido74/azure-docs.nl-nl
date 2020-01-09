---
title: 'Snelstartgids: een HoloLens-app maken met DirectX'
description: In deze quickstart leert u een HoloLens-app maken met behulp van Spatial Anchors.
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 01a9a8239e2997335f5123d63cfc664027cffa02
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75376355"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Snelstartgids: een HoloLens-app maken met behulp van Azure spatiale ankers, in C++/WinRT en DirectX

In deze quickstart wordt besproken hoe u een HoloLens-app maakt met behulp van [Azure Spatial Anchors](../overview.md) in C++/WinRT en DirectX. Azure Spatial Anchors is een platformoverstijgende ontwikkelaarsservice waarmee u mixed reality-ervaringen kunt maken met behulp van objecten die hun locatie in de loop van de tijd op meerdere apparaten behouden. Als u klaar bent, hebt u een HoloLens-app gemaakt waarmee een ruimtelijk anker kan worden opgeslagen en teruggehaald.

U leert het volgende:

> [!div class="checklist"]
> * Een Spatial Anchors-account maken
> * De Spatial Anchors-account-id en -accountsleutel configureren
> * Implementeren en uitvoeren op een HoloLens-apparaat

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:
- Een Windows-machine met <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> geïnstalleerd met de **universeel Windows-platform Development** -werk belasting en het onderdeel **Windows 10 SDK (10.0.18362.0 of hoger)** . U moet ook <a href="https://git-scm.com/download/win" target="_blank">git installeren voor Windows</a> en <a href="https://git-lfs.github.com/">Git LF</a>.
- De [C++/WinRT Visual Studio Extension (VSIX)](https://aka.ms/cppwinrt/vsix) voor Visual Studio moet worden geïnstalleerd vanuit de [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
- Een HoloLens-apparaat waarvoor de [ontwikkelaarsmodus](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) is ingeschakeld. Dit artikel vereist een HoloLens-apparaat met de [Update voor Windows van 10 oktober 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (ook wel bekend als RS5). Als u wilt bijwerken naar de nieuwste release op HoloLens, opent u de app **Instellingen**, gaat u naar **Bijwerken en beveiliging** en selecteert u vervolgens de knop **Controleren op updates**.
- Uw app moet de mogelijkheid **spatialPerception** instellen in het bijbehorende AppX-manifest.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Voorbeeldproject openen

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Open `HoloLens\DirectX\SampleHoloLens.sln` in Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Account-id en -sleutel configureren

De volgende stap is het configureren van de app voor het gebruik van uw account-id en de account sleutel. U hebt deze in een tekst editor gekopieerd bij [het instellen van de bron voor ruimtelijke ankers](#create-a-spatial-anchors-resource).

Open `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`.

Zoek het veld `SpatialAnchorsAccountKey` en vervang `Set me` met de accountsleutel.

Zoek het `SpatialAnchorsAccountId`-veld en vervang `Set me` met de account-id.

## <a name="deploy-the-app-to-your-hololens"></a>De app implementeren op uw HoloLens

Wijzig **Solution Configuration** in **Release**, wijzig **Solution Platform** in **x86** en selecteer **Device** uit de opties voor het implementatiedoel.

Als u HoloLens 2 gebruikt, gebruikt u **ARM64** als het **oplossings platform**in plaats van **x86**.

![Configuratie van Visual Studio](./media/get-started-hololens/visual-studio-configuration.png)

Start het HoloLens-apparaat, meld u aan en sluit het aan op de pc via een USB-kabel.

Selecteer **Debug** > **Start debugging** om de app te implementeren en de foutopsporing te starten.

Volg de instructies in de app om een anker te plaatsen en terug te halen.

Stop de app in Visual Studio door **Stop Debugging** te selecteren of door op **Shift+F5** te drukken.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Zelf studie: ruimtelijke ankers delen op meerdere apparaten](../tutorials/tutorial-share-anchors-across-devices.md)
