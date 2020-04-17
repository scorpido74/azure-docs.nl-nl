---
title: 'Snelstart: een HoloLens-app maken met Unity'
description: In deze quickstart leert u een HoloLens-app maken met Unity en met behulp van Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9e946d7f4616799768711ee8c18584a2a8fcff2a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81482012"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Snelstart: een Unity HoloLens-app maken die azure-ruimtelijke ankers gebruikt

In deze quickstart maakt u een Unity HoloLens-app die azure [spatial anchors gebruikt.](../overview.md) Spatial Anchors is een cross-platform developer service waarmee u mixed reality-ervaringen creëren met objecten die hun locatie in de loop van de tijd op verschillende apparaten blijven gebruiken. Als u klaar bent, hebt u een HoloLens-app met Unity gemaakt waarmee een ruimtelijk anker kan worden opgeslagen en teruggehaald.

U leert het volgende:

- Maak een account ruimtelijke ankers aan.
- Bereid de build-instellingen van Unity voor.
- Configureer de account-id ruimtelijke ankers en de accountsleutel.
- Exporteer het HoloLens Visual Studio-project.
- Implementeer de app en voer deze uit op een HoloLens-apparaat.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

- Je hebt een Windows-computer nodig met <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 of 2019.2</a> en <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> of hoger zijn geïnstalleerd. Uw Visual Studio-installatie moet de **algemene windows-platform-ontwikkelworkload** en het onderdeel **Windows 10 SDK (10.0.18362.0 of nieuwer)** bevatten. U moet ook <a href="https://git-scm.com/download/win" target="_blank">Git voor Windows</a> en <a href="https://git-lfs.github.com/">Git LFS</a>installeren.
- Je hebt een HoloLens-apparaat nodig waarop [de ontwikkelaarsmodus](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) is ingeschakeld. [Windows 10 October 2018 Update](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018) (ook bekend als RS5) moet op het apparaat worden geïnstalleerd. Als u wilt updaten naar de nieuwste versie op HoloLens, opent u de **app Instellingen,** gaat u **naar &-beveiliging bijwerken**en selecteert u Controleren op **updates**.
- In uw app moet u de **mogelijkheid Voor ruimtelijke perceptie** inschakelen. Deze instelling bevindt zich in**Publicatiemogelijkheden**Voor > **publicatieinstellingen** > **Publishing Settings** >  **voor bouwinstellingen**.
- In uw app moet u **Virtual Reality Supported** met **Windows Mixed Reality SDK**inschakelen. Deze instelling is in De > **bewaarinstellingen** >  **voor instellingen voor bouwinstellingen****.**

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Het voorbeeldproject Unity downloaden en openen

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

Open **Build Settings** door **File** > **Build Settings** te selecteren.

in de sectie **Platform** selecteert u **Universeel Windows-platform**. Wijzig het **doelapparaat** in **HoloLens**.

Selecteer **Switch Platform** om het platform te wijzigen in **Universeel Windows-platform**. Unity kan u vragen om UWP-ondersteuningscomponenten te installeren als deze ontbreken.

![Venster Instellingen voor eenheidsopbouw](./media/get-started-unity-hololens/unity-build-settings.png)

Sluit het venster **Build Settings**.

## <a name="configure-the-account-identifier-and-key"></a>De account-id en -sleutel configureren

Ga **Project** in het deelvenster `Assets/AzureSpatialAnchors.Examples/Scenes` Project `AzureSpatialAnchorsBasicDemo.unity` naar en open het scènebestand.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Sla de scène op door **Bestand** > **opslaan te**selecteren .

## <a name="export-the-hololens-visual-studio-project"></a>Het HoloLens Visual Studio-project exporteren

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selecteer **Bouwen**. Selecteer in het dialoogvenster een map waarin u het HoloLens Visual Studio-project wilt exporteren.

Wanneer de export is voltooid, verschijnt er een map met het geëxporteerde HoloLens-project.

## <a name="deploy-the-hololens-application"></a>HoloLens-toepassing implementeren

Dubbelklik in de map op **HelloAR U3D.sln** om het project in Visual Studio te openen.

Wijzig de **oplossingsconfiguratie** in **Release,** wijzig het **oplossingsplatform** in **x86**en selecteer **Apparaat** in de opties voor implementatiedoel.

Als u HoloLens 2 gebruikt, gebruikt u **ARM64** als **oplossingsplatform**, in plaats van **x86**.

   ![Visual Studio-configuratie](./media/get-started-unity-hololens/visual-studio-configuration.png)

Schakel het HoloLens-apparaat in, meld u aan en sluit het apparaat aan op de pc via een USB-kabel.

Selecteer Foutopsporing van **Foutopsporing** > **debuggen** om uw app te implementeren en te beginnen met foutopsporing.

Volg de instructies in de app om een anker te plaatsen en terug te halen.

Stop in Visual Studio de app door **Foutopsporing** of Shift+F5 te stoppen.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="unity-20193"></a>Eenheid 2019.3

Vanwege het breken van veranderingen wordt Unity 2019.3 momenteel niet ondersteund. Gebruik Unity 2019.1 of 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Zelfstudie: Ruimtelijke ankers delen op verschillende apparaten](../tutorials/tutorial-share-anchors-across-devices.md)
