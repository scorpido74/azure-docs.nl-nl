---
title: 'Snelstart: een Xamarin iOS-app maken'
description: In deze quickstart leer je hoe je een iOS-app met Xamarin bouwen met behulp van Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c5e217a33c8b461a438e2d0209fe6733850634e6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75465144"
---
# <a name="quickstart-create-a-xamarin-ios-app-with-azure-spatial-anchors"></a>Snelstart: een Xamarin iOS-app maken met Azure Spatial Anchors

Deze quickstart heeft betrekking op het maken van een iOS-app met Xamarin met [Azure Spatial Anchors.](../overview.md) Azure Spatial Anchors is een platformoverstijgende ontwikkelaarsservice waarmee u mixed reality-ervaringen kunt maken met behulp van objecten die hun locatie in de loop van de tijd op meerdere apparaten behouden. Als je klaar bent, heb je een iOS-app die een ruimtelijk anker kan opslaan en terugroepen.

U leert het volgende:

> [!div class="checklist"]
> * Een Spatial Anchors-account maken
> * De Spatial Anchors-account-id en -accountsleutel configureren
> * Implementeren en uitvoeren op een iOS-apparaat

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:
- Een Mac met macOS High Sierra (10.13) of hoger met:
  - De nieuwste versie van Xcode en iOS SDK geïnstalleerd vanuit de [App Store.](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)
  - Een up-to-date versie van <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio voor Mac 8.1+</a>.
  - <a href="https://git-scm.com/download/mac" target="_blank">Git voor macOS</a>.
  - <a href="https://git-lfs.github.com/">Git LFS</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Voorbeeldproject openen

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Open `Xamarin/SampleXamarin.sln` in Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Account-id en -sleutel configureren

De volgende stap is het configureren van de app om uw account-id en accountsleutel te gebruiken. U hebt ze gekopieerd naar een teksteditor bij [het instellen van de bron Ruimtelijke ankers](#create-a-spatial-anchors-resource).

Open `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Zoek het veld `SpatialAnchorsAccountKey` en vervang `Set me` met de accountsleutel.

Zoek het `SpatialAnchorsAccountId`-veld en vervang `Set me` met de account-id.

## <a name="deploy-the-app-to-your-ios-device"></a>De app implementeren op uw iOS-apparaat

Sluit het iOS-apparaat aan, meld u aan en sluit deze aan op de computer via een USB-kabel.

Stel het opstartproject in op **SampleXamarin.iOS,** wijzig de **oplossingsconfiguratie** in **Release**en selecteer het apparaat dat u wilt implementeren in de vervolgkeuzelijst voor apparatenselectie.

![Configuratie van Visual Studio](./media/get-started-xamarin-iOS/visual-studio-macos-configuration.jpg)

Selecteer **Start uitvoeren** > **zonder foutopsporing** om uw app te implementeren en te starten.

Selecteer in de app **Basic** om de demo uit te voeren en volg de instructies om een anker te plaatsen en terug te roepen.

> ![Screenshot](./media/get-started-xamarin-ios/screenshot-1.jpg)
> ![1](./media/get-started-xamarin-ios/screenshot-2.jpg)
> ![Screenshot 2 Screenshot 3](./media/get-started-xamarin-ios/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Zelfstudie: Ruimtelijke ankers delen op verschillende apparaten](../tutorials/tutorial-share-anchors-across-devices.md)
