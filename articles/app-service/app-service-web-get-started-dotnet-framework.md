---
title: Een C# ASP.NET Framework-web-app maken - Azure App Service| Microsoft Docs
description: Leer hoe u web-apps uitvoert in Azure App Service door de standaard C# ASP.NET-web-app te implementeren.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6c32415e750964e94129a4a6f9cf3812fe9117b5
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067279"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Een ASP.NET Framework-web-app maken in Azure

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie.  In deze snelstartgids ziet u hoe u uw eerste ASP.NET-web-app implementeert naar Azure App Service. Als u klaar bent, hebt u een resourcegroep die bestaat uit een App Service-plan en een Azure Service-app met een geïmplementeerde webtoepassing.

![](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

U moet <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> met de **ASP.NET- en webontwikkelworkload** installeren om deze zelfstudie te doorlopen.

Als u Visual Studio 2017 al hebt geïnstalleerd:

- Installeer de nieuwste updates in Visual Studio door te klikken op **Help** > **Check for Updates**.
- Voeg de workload toe door te klikken op **Tools** > **Get Tools and Features**.

## <a name="create-an-aspnet-web-app"></a>Een ASP.NET-web-app maken

Maak in Visual Studio een project door **Bestand > Nieuw > Project** te selecteren. 

Selecteer **Visual C# > Web > ASP.NET-webtoepassing (.NET Framework)** in het dialoogvenster **Nieuw project**.

Geef de toepassing de naam _myFirstAzureWebApp_ en selecteer vervolgens **OK**.
   
![Het dialoogvenster Nieuw project](./media/app-service-web-get-started-dotnet-framework/new-project.png)

U kunt elk type ASP.NET-web-app implementeren in Azure. Voor deze Quickstart selecteert u de sjabloon **MVC** en stelt u de verificatie in op **Geen verificatie**.
      
Selecteer **OK**.

![Het dialoogvenster Nieuw ASP.NET-project](./media/app-service-web-get-started-dotnet-framework/select-mvc-template.png)

Selecteer in het menu **Fouten opsporen > Starten zonder foutopsporing** om de web-app lokaal uit te voeren.

![De app lokaal uitvoeren](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## <a name="launch-the-publish-wizard"></a>Start de publicatiewizard

Klik in **Solution Explorer** met de rechtermuisknop op het project **myFirstAzureWebApp**-en selecteer **Publiceren**.

![Publiceren vanuit Solution Explorer](./media/app-service-web-get-started-dotnet-framework/solution-explorer-publish.png)

De publicatiewizard wordt automatisch gestart. Selecteer **App Service** > **Publish** om het dialoogvenster **Create App Service** te openen.

![Publiceren vanaf de projectoverzichtspagina](./media/app-service-web-get-started-dotnet-framework/publish-to-app-service.png)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Selecteer **Een account toevoegen** in het dialoogvenster **App Service maken** en meld u vervolgens aan bij uw Azure-abonnement. Als u al bent aangemeld, selecteert u het account met het gewenste abonnement uit de vervolgkeuzelijst.

> [!NOTE]
> Als u al bent aangemeld, selecteert u **Maken** nog niet.
>
>
   
![Aanmelden bij Azure](./media/app-service-web-get-started-dotnet-framework/sign-in-azure.png)

## <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Selecteer **Nieuw** naast **Resourcegroep**.

Geef de resourcegroep de naam **myResourceGroup** en selecteer **OK**.

## <a name="create-an-app-service-plan"></a>Een App Service-plan maken

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Selecteer bij **Hostingabonnement** **Nieuw**. 

Gebruik in het dialoogvenster **Hostingabonnement configureren** de instellingen uit de tabel na de onderstaande schermafbeelding.

![Een App Service-plan maken](./media/app-service-web-get-started-dotnet-framework/configure-app-service-plan.png)

| Instelling | Voorgestelde waarde | Description |
|-|-|-|
|App Service-plan| myAppServicePlan | De naam van het App Service-plan. |
| Location | Europa -west | Het datacenter waar de web-app wordt gehost. |
| Size | Gratis | De [prijscategorie](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) bepaalt de hosting-functies. |

Selecteer **OK**.

## <a name="create-and-publish-the-web-app"></a>De web-app maken en publiceren

Voer bij **App-naam** een unieke appnaam in (geldige tekens zijn `a-z`, `0-9` en `-`) of accepteer de automatisch gegenereerde unieke naam. De URL van de web-app is `http://<app_name>.azurewebsites.net`, waarbij `<app_name>` de naam van uw app is.

Selecteer **Maken** om de Azure-resources te gaan maken.

![Uw app-naam configureren](./media/app-service-web-get-started-dotnet-framework/web-app-name.png)

Zodra de wizard is voltooid, wordt de ASP.NET-web-app naar Azure gepubliceerd. Daarna wordt de app gestart in de standaardbrowser.

![Gepubliceerde ASP.NET-web-app in Azure](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

De naam van de app die is opgegeven in de [stap voor maken en publiceren](#create-and-publish-the-web-app), wordt gebruikt als URL-voorvoegsel in de indeling `http://<app_name>.azurewebsites.net`.

Gefeliciteerd, uw ASP.NET-web-app wordt live uitgevoerd in Azure App Service.

## <a name="update-the-app-and-redeploy"></a>De app bijwerken en opnieuw implementeren

Open vanuit de **Solution Explorer** _Views\Home\Index.cshtml_.

Zoek ergens bovenaan de HTML-tag `<div class="jumbotron">` en vervang het volledige element door de volgende code:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Als u opnieuw wilt implementeren naar Azure, klikt u in **Solution Explorer** met de rechtermuisknop op het project **myFirstAzureWebApp** en selecteert u **Publiceren**.

Selecteer **Publiceren** op de publicatiepagina.
![Pagina samenvatting publiceren in Visual Studio](./media/app-service-web-get-started-dotnet-framework/publish-summary-page.png)

Als het publiceren is voltooid, start Visual Studio een browser waarin de URL van de web-app wordt geladen.

![Bijgewerkte ASP.NET-web-app in Azure](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>De Azure-app beheren

Ga naar <a href="https://portal.azure.com" target="_blank">Azure Portal</a> om de web-app te beheren.

Selecteer in het linkermenu **App Services** en selecteer de naam van uw Azure-app.

![Navigatie naar Azure-app in de portal](./media/app-service-web-get-started-dotnet-framework/access-portal.png)

De pagina Overzicht van uw web-app wordt weergegeven. Hier kunt u algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen. 

![App Service-blade in Azure Portal](./media/app-service-web-get-started-dotnet-framework/web-app-blade.png)

Het linkermenu bevat een aantal pagina's voor het configureren van uw app. 

## <a name="video"></a>Video

Bekijk de video om de inhoud van deze snelstartgids in actie te zien en doorloop de stappen daarna zelf om uw eerste .NET-app op Azure te publiceren.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-for-NET-Developers/Create-a-NET-app-in-Azure-Quickstart/player]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [ASP.NET met SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
