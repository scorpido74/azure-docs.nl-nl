---
title: Een C# ASP.NET Core-web-app maken - Azure App Service | Microsoft Docs
description: Leer hoe u web-apps uitvoert in Azure App Service door de standaard C# ASP.NET Core-web-app te implementeren.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b64fd653a737201921ad481c50e2a72dc00cd912
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071755"
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Een ASP.NET Core-web-app maken in Azure

> [!NOTE]
> In dit artikel gaat u een app implementeren in App Service onder Windows. Zie [Een .NET Core-web-app maken en implementeren in App Service onder Linux ](./containers/quickstart-dotnetcore.md) om een app te implementeren in App Service onder _Linux_. 
>

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie.  In deze snelstart ziet u hoe u uw eerste ASP.NET Core-web-app implementeert naar Azure App Service. Als u klaar bent, hebt u een resourcegroep die bestaat uit een App Service-plan en een Azure Service-app met een geïmplementeerde webtoepassing.

![](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

U moet <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> met de **ASP.NET- en webontwikkelworkload** installeren om deze zelfstudie te doorlopen.

Als u Visual Studio 2017 al hebt geïnstalleerd:

- Installeer de nieuwste updates in Visual Studio door te klikken op **Help** > **Check for Updates**.
- Voeg de workload toe door te klikken op **Tools** > **Get Tools and Features**.

## <a name="create-an-aspnet-core-web-app"></a>Een ASP.NET Core-web-app maken

Maak in Visual Studio een project door **Bestand > Nieuw > Project** te selecteren. 

Selecteer **Visual C# > Web > ASP.NET Core-webtoepassing** in het dialoogvenster **Nieuw project**.

Geef de toepassing de naam _myFirstAzureWebApp_ en selecteer vervolgens **OK**.
   
![Het dialoogvenster Nieuw project](./media/app-service-web-get-started-dotnet/new-project.png)

U kunt elk type ASP.NET Core-web-app implementeren in Azure. Voor deze quickstart selecteert u de sjabloon **Web Application**, stelt u verificatie in op **No Authentication** en zorgt u dat er geen andere optie is geselecteerd.
      
Selecteer **OK**.

![Het dialoogvenster Nieuw ASP.NET-project](./media/app-service-web-get-started-dotnet/razor-pages-aspnet-dialog.png)

Selecteer in het menu **Fouten opsporen > Starten zonder foutopsporing** om de web-app lokaal uit te voeren.

![De app lokaal uitvoeren](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="launch-the-publish-wizard"></a>Start de publicatiewizard

Klik in **Solution Explorer** met de rechtermuisknop op het project **myFirstAzureWebApp**-en selecteer **Publiceren**.

![Publiceren vanuit Solution Explorer](./media/app-service-web-get-started-dotnet/right-click-publish.png)

De publicatiewizard wordt automatisch gestart. Selecteer **App Service** > **Publish** om het dialoogvenster **Create App Service** te openen.

![Publiceren vanaf de projectoverzichtspagina](./media/app-service-web-get-started-dotnet/publish-to-app-service.png)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Klik in het dialoogvenster **App Service maken** op **Een account toevoegen** en meld u vervolgens aan bij uw Azure-abonnement. Als u al bent aangemeld, selecteert u het account dat u wilt in de vervolgkeuzelijst.

> [!NOTE]
> Als u al bent aangemeld, selecteert u **Maken** nog niet.
>
   
![Aanmelden bij Azure](./media/app-service-web-get-started-dotnet/sign-in-azure.png)

## <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Selecteer **Nieuw** naast **Resourcegroep**.

Geef de resourcegroep de naam **myResourceGroup** en selecteer **OK**.

## <a name="create-an-app-service-plan"></a>Een App Service-plan maken

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Selecteer bij **Hostingabonnement** **Nieuw**. 

Gebruik in het dialoogvenster **Hostingabonnement configureren** de instellingen uit de tabel na de onderstaande schermafbeelding.

![Een App Service-plan maken](./media/app-service-web-get-started-dotnet/configure-app-service-plan.png)

| Instelling | Voorgestelde waarde | Description |
|-|-|-|
|App Service-plan| myAppServicePlan | De naam van het App Service-plan. |
| Location | Europa -west | Het datacenter waar de web-app wordt gehost. |
| Size | Gratis | De [prijscategorie](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) bepaalt de hosting-functies. |

Selecteer **OK**.

## <a name="create-and-publish-the-web-app"></a>De web-app maken en publiceren

Voer bij **App-naam** een unieke appnaam in (geldige tekens zijn `a-z`, `0-9` en `-`) of accepteer de automatisch gegenereerde unieke naam. De URL van de web-app is `http://<app_name>.azurewebsites.net`, waarbij `<app_name>` de naam van uw app is.

Selecteer **Maken** om de Azure-resources te gaan maken.

![Uw app-naam configureren](./media/app-service-web-get-started-dotnet/web-app-name.png)

Zodra de wizard is voltooid, wordt de ASP.NET Core-web-app naar Azure gepubliceerd. Daarna wordt de app gestart in de standaardbrowser.

![Gepubliceerde ASP.NET-web-app in Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

De naam van de app die is opgegeven in de [stap voor maken en publiceren](#create-and-publish-the-web-app), wordt gebruikt als URL-voorvoegsel in de indeling `http://<app_name>.azurewebsites.net`.

Gefeliciteerd, uw ASP.NET Core-web-app wordt live uitgevoerd in Azure App Service.

## <a name="update-the-app-and-redeploy"></a>De app bijwerken en opnieuw implementeren

Open vanuit de **Solution Explorer** _Pages/Index.cshtml_.

Vervang de twee `<div>`-tags door de volgende code:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Als u opnieuw wilt implementeren naar Azure, klikt u in **Solution Explorer** met de rechtermuisknop op het project **myFirstAzureWebApp** en selecteert u **Publiceren**.

Selecteer **Publiceren** op de pagina samenvatting publiceren.
![Pagina samenvatting publiceren in Visual Studio](./media/app-service-web-get-started-dotnet/publish-summary-page.png)

Als het publiceren is voltooid, start Visual Studio een browser waarin de URL van de web-app wordt geladen.

![Bijgewerkte ASP.NET-web-app in Azure](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-app"></a>De Azure-app beheren

Ga naar <a href="https://portal.azure.com" target="_blank">Azure Portal</a> om de web-app te beheren.

Selecteer in het linkermenu **App Services** en selecteer de naam van uw Azure-app.

![Navigatie naar Azure-app in de portal](./media/app-service-web-get-started-dotnet/access-portal.png)

De pagina Overzicht van uw web-app wordt weergegeven. Hier kunt u algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen. 

![App Service-blade in Azure Portal](./media/app-service-web-get-started-dotnet/web-app-blade.png)

Het linkermenu bevat een aantal pagina's voor het configureren van uw app. 

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [ASP.NET Core met SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
