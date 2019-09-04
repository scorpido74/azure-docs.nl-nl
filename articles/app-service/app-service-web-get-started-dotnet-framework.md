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
ms.date: 08/30/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 4f479ad60d74f1c7381b5fb776c5508aaa0785f1
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242149"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Een ASP.NET Framework-web-app maken in Azure

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie.

In deze snelstartgids ziet u hoe u uw eerste ASP.NET-web-app implementeert naar Azure App Service. Wanneer u klaar bent, hebt u een App Service plan. U hebt ook een App Service-app met een geïmplementeerde webtoepassing.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt volt ooien, installeert u <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> met de **ASP.net-en Web Development** -werk belasting.

Als u Visual Studio 2019 al hebt geïnstalleerd:

- Installeer de meest recente updates in Visual Studio door **Help** > **controleren op updates**te selecteren.
- Voeg de werk belasting toe door **extra** > hulp middelen**en functies**te selecteren.

## Een ASP.NET-Web-app maken<a name="create-and-publish-the-web-app"></a>

Maak een ASP.NET-Web-app door de volgende stappen uit te voeren:

1. Open Visual Studio en selecteer vervolgens **een nieuw project maken**.

1. In **een nieuw project maken**, zoek en kies **ASP.net Web Application (.NET Framework)** voor C#en selecteer vervolgens **volgende**.

1. Geef in **uw nieuwe project een**naam voor de toepassing _myFirstAzureWebApp_en selecteer vervolgens **maken**.

   ![Uw web-app-project configureren](./media/app-service-web-get-started-dotnet-framework/configure-web-app-project-framework.png)

1. U kunt elk type ASP.NET-web-app implementeren in Azure. Kies voor deze Quick Start de **MVC** -sjabloon. 

1. Zorg ervoor dat verificatie is ingesteld op **geen verificatie**. Selecteer **Maken**.

   ![ASP.NET-webtoepassing maken](./media/app-service-web-get-started-dotnet-framework/select-mvc-template-vs2019.png)

1. Selecteer in het Visual Studio-menu de optie **fout opsporing** > **starten zonder fout opsporing** om de web-app lokaal uit te voeren.

   ![De app lokaal uitvoeren](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## Uw web-app publiceren<a name="launch-the-publish-wizard"></a>

1. Klik in **Solution Explorer**met de rechter muisknop op het project **MyFirstAzureWebApp** en selecteer **publiceren**.

1. Kies **app service** en selecteer vervolgens **publiceren**.

   ![Publiceren vanaf de projectoverzichtspagina](./media/app-service-web-get-started-dotnet-framework/publish-app-framework-vs2019.png)

1. In **app service nieuwe maken**, zijn uw opties afhankelijk van of u al bent aangemeld bij Azure en of u een Visual Studio-account hebt gekoppeld aan een Azure-account. Selecteer **een account toevoegen** of **Aanmelden** om u aan te melden bij uw Azure-abonnement. Als u al bent aangemeld, selecteert u het gewenste account.

   > [!NOTE]
   > Als u al bent aangemeld, selecteert u **Maken** nog niet.
   >
   >

   ![Aanmelden bij Azure](./media/app-service-web-get-started-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. Selecteer voor **resource groep**de optie **Nieuw**.

1. In **naam van nieuwe resource groep**voert u *myResourceGroup* in en selecteert u **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Selecteer **Nieuw**voor het **hosting abonnement**.

1. Voer in het dialoog venster **hosting plan configureren** de waarden in van de volgende tabel en selecteer vervolgens **OK**.

   | Instelling | Voorgestelde waarde | Description |
   |-|-|-|
   |App Service-plan| myAppServicePlan | De naam van het App Service-plan. |
   | Location | Europa -west | Het datacenter waar de web-app wordt gehost. |
   | Size | Gratis | De [prijscategorie](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) bepaalt de hosting-functies. |

   ![Een App Service-plan maken](./media/app-service-web-get-started-dotnet-framework/app-service-plan-framework-vs2019.png)

1. Voer **bij naam**een unieke app-naam in die alleen de `a-z`geldige tekens bevat: `A-Z` `0-9`,, en `-`. U kunt de automatisch gegenereerde unieke naam accepteren. De URL van de web-app is `http://<app_name>.azurewebsites.net`, waarbij `<app_name>` de naam van uw app is.

   ![Uw app-naam configureren](./media/app-service-web-get-started-dotnet-framework/web-app-name-framework-vs2019.png)

1. Selecteer **Maken** om de Azure-resources te gaan maken.

Zodra de wizard is voltooid, wordt de ASP.NET-web-app naar Azure gepubliceerd. Daarna wordt de app gestart in de standaardbrowser.

![Gepubliceerde ASP.NET-web-app in Azure](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

De naam van de app die is opgegeven in de **app service nieuwe pagina maken** , wordt gebruikt als het `http://<app_name>.azurewebsites.net`URL-voor voegsel in de notatie.

**Gefeliciteerd!** Uw ASP.NET-Web-app wordt live uitgevoerd in Azure App Service.

## <a name="update-the-app-and-redeploy"></a>De app bijwerken en opnieuw implementeren

1. Open **in Solution Explorer** **weer gaven** > **Start** > **index. cshtml**in het project.

1. Zoek ergens bovenaan de HTML-tag `<div class="jumbotron">` en vervang het volledige element door de volgende code:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Als u opnieuw wilt implementeren naar Azure, klikt u in **Solution Explorer** met de rechtermuisknop op het project **myFirstAzureWebApp** en selecteert u **Publiceren**.

1. Selecteer op de pagina samen vatting **publiceren** de optie **publiceren**.

   ![Visual Studio-overzichts pagina voor publicatie](./media/app-service-web-get-started-dotnet-framework/publish-summary-page-framework-vs2019.png)

Als het publiceren is voltooid, start Visual Studio een browser waarin de URL van de web-app wordt geladen.

![Bijgewerkte ASP.NET-web-app in Azure](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>De Azure-app beheren

1. Ga naar <a href="https://portal.azure.com" target="_blank">Azure Portal</a> om de web-app te beheren.

1. Selecteer in het linkermenu **App Services** en selecteer de naam van uw Azure-app.

   ![Navigatie naar Azure-app in de portal](./media/app-service-web-get-started-dotnet-framework/access-portal-framework-vs2019.png)

   De pagina Overzicht van uw web-app wordt weergegeven. Hier kunt u het basis beheer, zoals bladeren, stoppen, starten, opnieuw starten en verwijderen.

   ![App Service overzicht in Azure Portal](./media/app-service-web-get-started-dotnet-framework/web-app-general-framework-vs2019.png)

   Het linkermenu bevat een aantal pagina's voor het configureren van uw app.

## <a name="video"></a>Video

Bekijk de video om de inhoud van deze snelstartgids in actie te zien en doorloop de stappen daarna zelf om uw eerste .NET-app op Azure te publiceren.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-for-NET-Developers/Create-a-NET-app-in-Azure-Quickstart/player]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [ASP.NET met SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
