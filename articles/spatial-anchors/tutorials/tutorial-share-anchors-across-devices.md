---
title: 'Zelfstudie: Ankers delen tussen sessies en apparaten'
description: In deze zelfstudie leert u hoe u Azure Spatial Anchor-id's deelt tussen Android/iOS-apparaten in Unity met een back-endservice.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3b377f87bdba40c90cb3af6caef2c089d7b7de49
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77615474"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>Zelfstudie: Azure-ruimtelijke ankers delen voor sessies en apparaten

In deze zelfstudie leert u hoe u [Azure Spatial Anchors](../overview.md) gebruiken om tijdens één sessie ankers te maken en ze vervolgens te lokaliseren, op hetzelfde apparaat of op een ander apparaat. Dezelfde ankers kunnen ook worden gelokaliseerd door meerdere apparaten op dezelfde plaats en op hetzelfde moment.

![Persistentie](./media/persistence.gif)

Azure Spatial Anchors is een platformoverstijgende ontwikkelaarsservice waarmee u mixed reality-ervaringen kunt maken met behulp van objecten die hun locatie in de loop van de tijd op meerdere apparaten behouden. Wanneer u klaar bent, hebt u een app die kan worden geïmplementeerd op twee of meer apparaten. Azure Spatial Anchors dat door één exemplaar is gemaakt, kan worden gedeeld met andere.

U leert het volgende:

> [!div class="checklist"]
> * Implementeer een ASP.NET Core-web-app in Azure die kan worden gebruikt voor het delen van bladwijzers en deze opslaat in het geheugen voor een bepaalde tijd.
> * Configureer de scène AzureSpatialAnchorsLocalSharedDemo binnen het Unity-voorbeeld van onze Quickstarts om de web-app Sharing Anchors te kunnen gebruiken.
> * Implementeer en voer uit op een of meer apparaten.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Houd er rekening mee dat, hoewel u in deze zelfstudie gebruik zult maken van Unity en een ASP.NET Core-web-app, dit alleen een voorbeeld is om te laten zien hoe het delen van Azure Spatial Anchor-id's naar andere apparaten werkt. U andere talen en back-endtechnologieën gebruiken om hetzelfde doel te bereiken. De ASP.NET Core-web-app die wordt gebruikt in deze zelfstudie heeft bovendien een afhankelijkheid van .NET Core 2.2 SDK. Het werkt goed op reguliere Azure Web Apps (Windows), maar werkt momenteel niet in Azure Web Apps voor Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Het voorbeeldproject downloaden

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>Sharing Anchors Service implementeren

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Open Visual Studio en open het project in de map `Sharing\SharingServiceSample`.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

U moet een resourcegroep en een App-serviceplan maken voordat u de service implementeert in VS-code.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Navigeer naar de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a> en meld u aan bij uw Azure-abonnement.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Selecteer **Nieuw** naast **Resourcegroep**.

Geef de resourcegroep de naam **myResourceGroup** en selecteer **OK**.

### <a name="create-an-app-service-plan"></a>Een App Service-plan maken

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Selecteer bij **Hostingabonnement****Nieuw**.

Gebruik de volgende instellingen in het dialoogvenster **Hostingplan configureren:**

| Instelling | Voorgestelde waarde | Beschrijving |
|-|-|-|
|App Service-plan| MySharingServicePlan | De naam van het App Service-plan. |
| Locatie | VS - west | Het datacenter waar de web-app wordt gehost. |
| Grootte | Gratis | De [prijslaag](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) die hostingfuncties bepaalt. |

Selecteer **OK**.

Open Visual Studio Code en open `Sharing\SharingServiceSample` het project in de map. Volg <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">deze zelfstudie</a> om de deelservice te implementeren via Visual Studio Code. U de stappen volgen vanaf de sectie 'Open het met Visual Studio Code'. Maak geen ander mvc-project zoals uitgelegd in de bovenstaande stap, omdat u het project al hebt dat moet worden geïmplementeerd en gepubliceerd: de SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>De voorbeeld-app implementeren

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="unity-20193"></a>Eenheid 2019.3

Vanwege het breken van veranderingen wordt Unity 2019.3 momenteel niet ondersteund. Gebruik Unity 2019.1 of 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een ASP.NET Core-web-app in Azure geïmplementeerd en vervolgens geconfigureerd en een Unity-app geïmplementeerd. U hebt Spatial Anchors met de app gemaakt en gedeeld met andere apparaten met behulp van uw ASP.NET Core-web-app.

U uw ASP.NET Core Web App verbeteren, zodat deze Azure Cosmos DB gebruikt om de opslag van uw gedeelde Classificaties voor ruimtelijk anker te blijven opslaan. Als u Azure Cosmos DB-ondersteuning toevoegt, kan uw ASP.NET Core Web App vandaag een anker maken en dagen later terugkomen om het opnieuw te kunnen vinden, met behulp van de anker-id die is opgeslagen in uw web-app.

> [!div class="nextstepaction"]
> [Azure Cosmo DB gebruiken om ankers op te slaan](./tutorial-use-cosmos-db-to-store-anchors.md)

