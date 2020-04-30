---
title: Overzicht van Azure Spatial Anchors
description: Meer informatie over hoe Azure Spatial Anchors u helpt bij ontwikkelen van platformoverschrijdende mixed reality-ervaringen.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 5ebd29b5fb6fdedcdfbc434209b350512c4cd5dc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77657304"
---
# <a name="azure-spatial-anchors-overview"></a>Overzicht van Azure Spatial Anchors

Welkom bij Azure Spatial Anchors. Azure Spatial Anchors biedt ontwikkelaars essentiële mogelijkheden om mixed reality-toepassingen voor ruimtelijk inzicht te bouwen. Deze toepassingen kunnen ondersteuning bieden voor Microsoft HoloLens, iOS-apparaten die ARKit ondersteunen en Android-apparaten die ARCore ondersteunen. Azure Spatial Anchors biedt ontwikkelaars de mogelijkheid om te werken met mixed reality-platforms om ruimten te signaleren, interessante locaties aan te geven en die locaties op te halen op ondersteunde apparaten.
Deze interessante locaties worden ook wel Spatial Anchors genoemd.

![Platformoverschrijdend](./media/cross-platform.png)

## <a name="examples"></a>Voorbeelden

Enkele voorbeelden van gebruik dat mogelijk wordt gemaakt door Spatial Anchors zijn onder andere:

- [Ervaringen voor meerdere gebruikers](tutorials/tutorial-share-anchors-across-devices.md). Met Spatial Anchors kunnen mensen op dezelfde locatie deelnemen aan mixed reality-toepassingen voor meerdere gebruikers. Twee personen kunnen bijvoorbeeld een mixed reality-schaakspel spelen door een virtueel schaakbord op een tafel te zetten. Vervolgens kunnen ze door hun apparaat op de tafel te richten het virtuele schaakspel zien en samen een schaakpartij spelen.

- [Way-finding](concepts/anchor-relationships-way-finding.md). Ontwikkelaars kunnen Spatial Anchors ook samen verbinden en er relaties tussen maken. Een app kan bijvoorbeeld een ervaring bevatten die twee of meer interessante locaties heeft waarmee een gebruiker moet communiceren om een taak te voltooien. Deze interessante locaties kunnen met elkaar worden verbonden. Later, wanneer de gebruiker de uit meerdere stappen bestaande taak heeft voltooid, kan de app vragen om ankers die zich in de buurt van het huidige bevinden om de gebruiker naar de volgende stap in de taak te leiden.

- [Virtuele inhoud toepassen op de realiteit](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor). Een app kan toestaan dat een gebruiker een virtuele agenda op een prikbord in een conferentieruimte plaatst, die mensen kunnen zien met een telefoon-app of een HoloLens-apparaat. In een industriële omgeving kan een gebruiker contextuele informatie over een machine ontvangen door er met een ondersteunde apparaatcamera op te wijzen.

Azure Spatial Anchors bestaat uit een beheerde service en client-SDK's voor ondersteunde apparaatplatforms. De volgende secties bevatten informatie over hoe u aan de slag kunt gaan met het bouwen van apps met behulp van Azure Spatial Anchors.

## <a name="next-steps"></a>Volgende stappen

Uw eerste app maken met Spatial Anchors.

> [!div class="nextstepaction"]
> [Eenheid (HoloLens)](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](quickstarts/get-started-xamarin-ios.md)
