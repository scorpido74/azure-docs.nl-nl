---
title: 'Snelstart: maak een Xamarin Android-app'
description: In deze quickstart leer je hoe je een Android-app met Xamarin bouwen met behulp van Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 94906d645ec38a24d54536ee8aa93e7418c8dc35
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75465174"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>Snelstart: een Xamarin Android-app maken met Azure Spatial Anchors

Deze quickstart heeft betrekking op het maken van een Android-app met Xamarin met [Azure Spatial Anchors.](../overview.md) Azure Spatial Anchors is een platformoverstijgende ontwikkelaarsservice waarmee u mixed reality-ervaringen kunt maken met behulp van objecten die hun locatie in de loop van de tijd op meerdere apparaten behouden. Als je klaar bent, heb je een Android-app die een ruimtelijk anker kan opslaan en terugroepen.

U leert het volgende:

> [!div class="checklist"]
> * Een Spatial Anchors-account maken
> * De Spatial Anchors-account-id en -accountsleutel configureren
> * Implementeren en uitvoeren op een Android-apparaat

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:
- Een Windows- of macOS-computer:
  - Als u Windows gebruikt:
    - Een up-to-date versie van <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 16.2+</a>.
    - <a href="https://git-scm.com/download/win" target="_blank">Git voor Windows</a>.
    - <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Als u macOS gebruikt:
    - Een up-to-date versie van <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio voor Mac 8.1+</a>.
    - <a href="https://git-scm.com/download/mac" target="_blank">Git voor macOS</a>.
    - <a href="https://git-lfs.github.com/">Git LFS</a>.
- De nieuwste versie van Xamarin.Android geïnstalleerd en uitgevoerd op uw platform van keuze. Voor een gids voor het installeren van Xamarin.Android, verwijzen naar de [Xamarin.Android Installatie](https://docs.microsoft.com/xamarin/android/get-started/installation/index) gidsen.
- Een <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">door een ontwikkelaar geactiveerde</a> en <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">voor ARCore geschikt</a> Android-apparaat.
  - Mogelijk zijn er extra apparaatstuurprogramma's vereist om uw computer te laten communiceren met uw Android-apparaat. Voor meer informatie, zie [hier](https://developer.android.com/studio/run/device.html).
- Uw app moet zich richten op ARCore **1.8**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Voorbeeldproject openen

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Open `Xamarin/SampleXamarin.sln` in Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Account-id en -sleutel configureren

De volgende stap is het configureren van de app om uw account-id en accountsleutel te gebruiken. U hebt ze gekopieerd naar een teksteditor bij [het instellen van de bron Ruimtelijke ankers](#create-a-spatial-anchors-resource).

Open `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Zoek het veld `SpatialAnchorsAccountKey` en vervang `Set me` met de accountsleutel.

Zoek het `SpatialAnchorsAccountId`-veld en vervang `Set me` met de account-id.

## <a name="deploy-the-app-to-your-android-device"></a>De app op uw Android-apparaat implementeren

Sluit het Android-apparaat aan, meld u aan en sluit deze aan op de computer via een USB-kabel.

Stel het opstartproject in op **SampleXamarin.Android,** wijzig de **oplossingsconfiguratie** in **Release**en selecteer het apparaat dat u wilt implementeren in de vervolgkeuzelijst voor apparatenselectie.

# <a name="windows"></a>[Windows](#tab/deploy-windows)

![Configuratie van Visual Studio](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

Selecteer **Foutopsporing** > van Foutopsporing**debuggen om** uw app te implementeren en te starten.

# <a name="macos"></a>[Macos](#tab/deploy-macos)

![Configuratie van Visual Studio](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

Selecteer **Start uitvoeren** > **zonder foutopsporing** om uw app te implementeren en te starten.

---

Selecteer in de app **Basic** om de demo uit te voeren en volg de instructies om een anker te plaatsen en terug te roepen.

> ![Screenshot](./media/get-started-xamarin-android/screenshot-1.jpg)
> ![1](./media/get-started-xamarin-android/screenshot-2.jpg)
> ![Screenshot 2 Screenshot 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Zelfstudie: Ruimtelijke ankers delen op verschillende apparaten](../tutorials/tutorial-share-anchors-across-devices.md)
