---
title: 'Quickstart: Een C# ASP.NET Core-app maken'
description: Leer hoe u web-apps uitvoert in Azure App Service door de standaard C# ASP.NET Core-web-app te implementeren vanuit Visual Studio.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: d489ed9dbf9b8075951258e0841675877e11be38
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506788"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Quickstart: Een ASP.NET Core-web-app maken in Azure

In deze quickstart leert u hoe u uw eerste ASP.NET Core-web-app maakt en implementeert in [Azure App Service](overview.md). 

Als u klaar bent, beschikt u over een Azure-resourcegroep die bestaat uit een App Service-hostingabonnement en een Azure Service-app met een geïmplementeerde webtoepassing.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/dotnet/)
- In deze quickstart implementeert u een app in App Service onder Windows. Zie [Een .NET Core-web-app maken en implementeren in App Service onder Linux ](./containers/quickstart-dotnetcore.md) om een app te implementeren in App Service onder _Linux_.
- U moet <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> met de **ASP.NET- en webontwikkelworkload** installeren.

  Als u Visual Studio 2019 al hebt geïnstalleerd:

  - Installeer de nieuwste updates in Visual Studio door **Help** > **Controleren op updates** te selecteren.
  - Voeg de workload toe door **Hulpprogramma's** > **Hulpprogramma's en functies ophalen** te selecteren.


## <a name="create-an-aspnet-core-web-app"></a>Een ASP.NET Core-web-app maken

Maak een ASP.NET Core-web-app in Visual Studio door deze stappen te volgen:

1. Open Visual Studio en selecteer **Een nieuw project maken**.

1. Selecteer in **Een nieuw project maken** de **ASP.NET Core-webtoepassing**, en controleer of **C#** wordt vermeld in de talen voor deze keuze. Selecteer vervolgens **Volgende**.

1. Geef in **Uw nieuwe project configureren** het webtoepassingsproject de naam *myFirstAzureWebApp*, en selecteer **Maken**.

   ![Uw web-app-project configureren](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. U kunt elk type ASP.NET Core-web-app implementeren in Azure, maar voor deze quickstart kiest u de sjabloon **Webtoepassing**. Zorg ervoor dat **Verificatie** is ingesteld op **Geen verificatie** en dat er geen andere optie is geselecteerd. Ten slotte selecteert u **Create**.

   ![Een nieuw ASP.NET Core-web-app maken](./media/app-service-web-get-started-dotnet/create-aspnet-core-web-app.png) 
   
1. Selecteer in het menu van Visual Studio de optie **Foutopsporing** > **Starten zonder foutopsporing** om de web-app lokaal uit te voeren.

   ![Web-app die lokaal wordt uitgevoerd](./media/app-service-web-get-started-dotnet/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Uw web-app publiceren

Als u de web-app wilt publiceren, moet u eerst een nieuwe App Service maken en configureren waarin u de app kunt publiceren. 

Als onderdeel van het instellen van de App Service maakt u het volgende:

- Een nieuwe [resource groep](https://docs.microsoft.com/azure/azure-resource-manager/management/overview#terminology) die alle Azure-resources voor de service bevat.
- Een nieuw [Hostingabonnement](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) dat de locatie, grootte en functies opgeeft van de webserverfarm die als host fungeert voor de app.

Volg deze stappen om de App Service te maken en uw web-app te publiceren:

1. Klik in **Solution Explorer** met de rechtermuisknop op het project **myFirstAzureWebApp** en selecteer **Publiceren**. Als u zich nog niet hebt aangemeld bij uw Azure-account vanuit Visual Studio, selecteert u **Een account toevoegen** of **Aanmelden**. U kunt ook een gratis Azure-account maken.

1. Kies in het dialoogvenster **Een publicatiedoel kiezen** de optie **App Service**, selecteer **Nieuwe maken** en selecteer vervolgens **Profiel maken**.

   ![Kies een publicatiedoel](./media/app-service-web-get-started-dotnet/pick-publish-target-vs2019.png)

1. Geef in het dialoogvenster **App Service: Nieuwe App Service maken**, een globaal unieke **Naam** op voor de app door de standaardnaam te accepteren of een nieuwe naam in te voeren. Geldige tekens zijn `a-z`, `A-Z`, `0-9` en `-`. Deze **Naam** wordt gebruikt als het URL-voorvoegsel voor de web-app in de indeling `http://<app_name>.azurewebsites.net`.

1. Accepteer bij **Abonnement** het abonnement dat wordt vermeld, of selecteer een nieuw abonnement in de vervolgkeuzelijst.

1. Selecteer in **Resourcegroep** de optie **Nieuw**. Voer bij **Naam van nieuwe resourcegroep** in: *myResourceGroup*. Selecteer vervolgens **OK**. 

1. Selecteer bij **Hostingabonnement** de optie **Nieuw**. 

1. Voer in het dialoogvenster **Hostingabonnement: Nieuw hostingabonnement maken** de waarden in die zijn opgegeven in de volgende tabel:

   | Instelling  | Voorgestelde waarde | Beschrijving |
   | -------- | --------------- | ----------- |
   | **Hostingabonnement**  | *myFirstAzureWebAppPlan* | De naam van het App Service-plan. |
   | **Locatie**      | *Europa -west* | Het datacenter waar de web-app wordt gehost. |
   | **Grootte**          | *Gratis* | De [prijscategorie](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) bepaalt de hosting-functies. |
   
   ![Nieuw hostingabonnement maken](./media/app-service-web-get-started-dotnet/create-new-hosting-plan-vs2019.png)

1. Houd **Application Insights** ingesteld op *Geen*.

1. Selecteer in het dialoogvenster **App Service: Nieuwe App Service maken** de optie **Maken** om te beginnen met het maken van de Azure-resources.

   ![Nieuwe App Service maken](./media/app-service-web-get-started-dotnet/create-new-app-service-vs2019.png)

1. Zodra de wizard is voltooid, selecteert u **Publiceren**.

   ![Web-app publiceren in Azure](./media/app-service-web-get-started-dotnet/publish-web-app-vs2019.png)

   Met Visual Studio wordt uw ASP.NET Core web-app gepubliceerd in Azure. De app wordt gestart in de standaardbrowser. 

   ![Gepubliceerde ASP.NET-web-app, uitgevoerd in Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

**Gefeliciteerd!** De ASP.NET-web-app wordt live uitgevoerd in Azure App Service.

## <a name="update-the-app-and-redeploy"></a>De app bijwerken en opnieuw implementeren

Volg deze stappen om uw web-app bij te werken en opnieuw te implementeren:

1. Open in **Solution Explorer**, onder uw project, achtereenvolgens **Pagina’s** > **Index.cshtml**.

1. Vervang de hele tag `<div>` door de volgende code:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Als u opnieuw wilt implementeren naar Azure, klikt u in **Solution Explorer** met de rechtermuisknop op het project **myFirstAzureWebApp** en selecteert u **Publiceren**.

1. Selecteer op de samenvattingspagina **Publiceren** de optie **Publiceren**.

   ![Update voor web-app publiceren](./media/app-service-web-get-started-dotnet/publish-update-to-web-app-vs2019.png)

Als het publiceren is voltooid, start Visual Studio een browser waarin de URL van de web-app wordt geladen.

![Bijgewerkte ASP.NET-web-app, uitgevoerd in Azure](./media/app-service-web-get-started-dotnet/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>De Azure-app beheren

Als u de web-app wilt beheren, gaat u naar de [Azure-portal](https://portal.azure.com) en selecteert u **App Services**.

![App Services selecteren](./media/app-service-web-get-started-dotnet/app-services.png)

Selecteer op de pagina **App Services** de naam van uw web-app.

![Navigatie naar Azure-app in de portal](./media/app-service-web-get-started-dotnet/select-app-service.png)

De **Overzichtspagina** voor de web-app bevat opties voor basisbeheer, zoals browsen, stoppen, starten, opnieuw starten en verwijderen. Het linkermenu bevat een meer pagina's voor het configureren van uw app.

![App Service in de Azure-portal](./media/app-service-web-get-started-dotnet/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u Visual Studio gebruikt om een ASP.NET Core-webtoepassing te maken en implementeren in Azure App Service.

Ga door met het volgende artikel om te leren hoe u een .NET Core-app maakt en deze verbindt met een SQL-database:

> [!div class="nextstepaction"]
> [ASP.NET Core met SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)

> [!div class="nextstepaction"]
> [ASP.NET Core-app configureren](configure-language-dotnetcore.md)

Wilt u uw clouduitgaven optimaliseren en geld besparen?

> [!div class="nextstepaction"]
> [Analyseer kosten met Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)