---
title: 'Zelf studie: ankers delen tussen sessies en apparaten'
description: In deze zelf studie leert u hoe u Azure ruimtelijk-anker-id's tussen Android-en iOS-apparaten in unit met een back-end-service kunt delen.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3b377f87bdba40c90cb3af6caef2c089d7b7de49
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77615474"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Zelf studie: ruimtelijke Azure-ankers delen in sessies en apparaten

In deze zelf studie leert u hoe u met behulp van [Azure spatiale ankers](../overview.md) maakt ankers tijdens één sessie en deze vervolgens kunt vinden op hetzelfde apparaat of op een andere locatie. Deze ankers kunnen ook op dezelfde locatie en tegelijkertijd op meerdere apparaten worden gevonden.

![Persistentie](./media/persistence.gif)

Azure Spatial Anchors is een platformoverstijgende ontwikkelaarsservice waarmee u mixed reality-ervaringen kunt maken met behulp van objecten die hun locatie in de loop van de tijd op meerdere apparaten behouden. Wanneer u klaar bent, hebt u een app die kan worden geïmplementeerd op twee of meer apparaten. Azure Spatial Anchors dat door één exemplaar is gemaakt, kan worden gedeeld met andere.

U leert het volgende:

> [!div class="checklist"]
> * Implementeer een ASP.NET Core-web-app in Azure die kan worden gebruikt voor het delen van bladwijzers en deze opslaat in het geheugen voor een bepaalde tijd.
> * Configureer de scène AzureSpatialAnchorsLocalSharedDemo binnen het Unity-voorbeeld van onze Quickstarts om de web-app Sharing Anchors te kunnen gebruiken.
> * Implementeer en voer uit op een of meer apparaten.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Houd er rekening mee dat, hoewel u in deze zelfstudie gebruik zult maken van Unity en een ASP.NET Core-web-app, dit alleen een voorbeeld is om te laten zien hoe het delen van Azure Spatial Anchor-id's naar andere apparaten werkt. U kunt andere talen en back-end-technologieën gebruiken om hetzelfde doel te krijgen. De ASP.NET Core-web-app die wordt gebruikt in deze zelfstudie heeft bovendien een afhankelijkheid van .NET Core 2.2 SDK. Het werkt goed op reguliere Azure Web Apps (Windows), maar werkt momenteel niet in Azure Web Apps voor Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Het voorbeeld project downloaden

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Sharing Anchors Service implementeren

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Open Visual Studio en open het project in de map `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

U moet een resource groep en een App Service plan maken voordat u de service in VS code implementeert.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Navigeer naar het <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> en meld u aan bij uw Azure-abonnement.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Selecteer **Nieuw** naast **Resourcegroep**.

Geef de resourcegroep de naam **myResourceGroup** en selecteer **OK**.

### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Selecteer bij **Hostingabonnement****Nieuw**.

Gebruik in het dialoog venster **hosting plan configureren** de volgende instellingen:

| Instelling | Voorgestelde waarde | Beschrijving |
|-|-|-|
|App Service-plan| MySharingServicePlan | De naam van het App Service-plan. |
| Locatie | VS - west | Het datacenter waar de web-app wordt gehost. |
| Grootte | Gratis | De [prijs categorie](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) die de hosting functies bepaalt. |

Selecteer **OK**.

Open Visual Studio code en open het project in de `Sharing\SharingServiceSample` map. Volg <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">deze zelf studie</a> om de service voor delen te implementeren met Visual Studio code. U kunt de stappen volgen die worden gestart vanuit de sectie ' openen met Visual Studio code '. Maak geen ander MVC-project zoals uitgelegd in de bovenstaande stap, zoals u al het project hebt dat moet worden geïmplementeerd en gepubliceerd: de SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>De voor beeld-app implementeren

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="unity-20193"></a>Unity 2019,3

Als gevolg van het verbreken van wijzigingen, wordt unit 2019,3 momenteel niet ondersteund. Gebruik unit 2019,1 of 2019,2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een ASP.NET Core-web-app in Azure geïmplementeerd en vervolgens geconfigureerd en een Unity-app geïmplementeerd. U hebt Spatial Anchors met de app gemaakt en gedeeld met andere apparaten met behulp van uw ASP.NET Core-web-app.

U kunt uw ASP.NET Core web-app verbeteren zodat deze Azure Cosmos DB gebruikt om de opslag van de gedeelde ruimtelijk-anker-id's te behouden. Door Azure Cosmos DB ondersteuning toe te voegen, kan uw ASP.NET Core-web-app vandaag nog een anker maken en later terugkeren om het opnieuw te kunnen vinden met behulp van de anker-id die is opgeslagen in uw web-app.

> [!div class="nextstepaction"]
> [Azure Cosmo DB gebruiken om ankers op te slaan](./tutorial-use-cosmos-db-to-store-anchors.md)

