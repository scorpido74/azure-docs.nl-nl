---
title: 'Snelstartgids: een C# ASP.NET Core-app maken'
description: Meer informatie over het uitvoeren van web-apps in Azure App Service door de standaard C# ASP.NET Core web app-sjabloon te implementeren vanuit Visual Studio.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 6c7ab1290033792cf356c4882811670011df5efe
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82086042"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Snelstartgids: een ASP.NET Core-web-app maken in azure

In deze Quick Start leert u hoe u uw eerste ASP.NET Core Web-App kunt maken en implementeren op [Azure app service](overview.md). 

Wanneer u klaar bent, hebt u een Azure-resource groep die bestaat uit een App Service hosting abonnement en een App Service met een geïmplementeerde webtoepassing.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/dotnet/).
- In deze Snelstartgids wordt een app geïmplementeerd voor het App Service van Windows. Zie [Een .NET Core-web-app maken en implementeren in App Service onder Linux ](./containers/quickstart-dotnetcore.md) om een app te implementeren in App Service onder _Linux_.
- Installeer <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> met de **ASP.net-en Web Development** -werk belasting.

  Als u Visual Studio 2019 al hebt geïnstalleerd:

  - Installeer de meest recente updates in Visual Studio door **Help** > **controleren op updates**te selecteren.
  - Voeg de werk belasting toe door **extra** > hulp middelen**en functies**te selecteren.


## <a name="create-an-aspnet-core-web-app"></a>Een ASP.NET Core-web-app maken

Maak een ASP.NET Core web-app in Visual Studio door de volgende stappen uit te voeren:

1. Open Visual Studio en selecteer **een nieuw project maken**.

1. Selecteer in **een nieuw project maken de**optie **ASP.net core webtoepassing** en controleer of **C#** wordt vermeld in de talen voor die keuze, en selecteer vervolgens **volgende**.

1. Geef in **uw nieuwe project**de naam *myFirstAzureWebApp*van uw Web Application project op en selecteer **maken**.

   ![Uw web-app-project configureren](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. U kunt elk type ASP.NET Core web-app implementeren in azure, maar voor deze Snelstartgids kiest u de sjabloon **Webtoepassing** . Zorg ervoor dat **verificatie** is ingesteld op **geen verificatie**en dat er geen andere optie is geselecteerd. Ten slotte selecteert u **Create**.

   ![Een nieuwe ASP.NET Core-web-app maken](./media/app-service-web-get-started-dotnet/create-aspnet-core-web-app.png) 
   
1. Selecteer in het Visual Studio-menu de optie **fout opsporing** > **starten zonder fout opsporing** om uw web-app lokaal uit te voeren.

   ![Lokaal uitgevoerde web-app](./media/app-service-web-get-started-dotnet/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Uw web-app publiceren

Als u uw web-app wilt publiceren, moet u eerst een nieuwe App Service maken en configureren waarnaar u uw app kunt publiceren. 

Als onderdeel van het instellen van de App Service maakt u het volgende:

- Een nieuwe [resource groep](https://docs.microsoft.com/azure/azure-resource-manager/management/overview#terminology) die alle Azure-resources voor de service bevat.
- Een nieuw [hosting plan](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) waarmee de locatie, grootte en functies van de webserver farm worden opgegeven die als host fungeert voor uw app.

Volg deze stappen om uw App Service te maken en uw web-app te publiceren:

1. Klik in **Solution Explorer**met de rechter muisknop op het project **MyFirstAzureWebApp** en selecteer **publiceren**. Als u zich nog niet hebt aangemeld bij uw Azure-account vanuit Visual Studio, selecteert u **een account toevoegen** of **Aanmelden**. U kunt ook een gratis Azure-account maken.

1. Kies in het dialoog venster **een publicatie doel** selecteren **app service**, selecteer **nieuwe maken**en selecteer vervolgens **profiel maken**.

   ![Kies een publicatiedoel](./media/app-service-web-get-started-dotnet/pick-publish-target-vs2019.png)

1. Geef in het dialoog venster **app service: nieuwe maken** een wereld wijd unieke **naam** op voor uw app door de standaard naam te accepteren of door een nieuwe naam in te voeren. Geldige tekens zijn: `a-z`, `A-Z`, `0-9`en `-`. Deze **naam** wordt gebruikt als het URL-voor voegsel voor uw web-app `http://<app_name>.azurewebsites.net`in de indeling.

1. Voor het **abonnement**accepteert u het abonnement dat wordt vermeld of selecteert u een nieuw in de vervolg keuzelijst.

1. Selecteer in **resource groep**de optie **Nieuw**. In **naam van nieuwe resource groep**voert u *myResourceGroup* in en selecteert u **OK**. 

1. Selecteer **Nieuw**voor het **hosting abonnement**. 

1. Voer in het **hosting plan: nieuw** dialoog venster maken de waarden in die zijn opgegeven in de volgende tabel:

   | Instelling  | Voorgestelde waarde | Beschrijving |
   | -------- | --------------- | ----------- |
   | **Hostingabonnement**  | *myFirstAzureWebAppPlan* | De naam van het App Service-plan. |
   | **Locatie**      | *Europa -west* | Het datacenter waar de web-app wordt gehost. |
   | **Grootte**          | *Gratis* | De [prijscategorie](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) bepaalt de hosting-functies. |
   
   ![Nieuw hosting plan maken](./media/app-service-web-get-started-dotnet/create-new-hosting-plan-vs2019.png)

1. Stel **Application Insights** op *geen*in.

1. Selecteer in het dialoog venster **app service: nieuwe maken** de optie **maken** om de Azure-resources te maken.

   ![Nieuwe app service maken](./media/app-service-web-get-started-dotnet/create-new-app-service-vs2019.png)

1. Wanneer de wizard is voltooid, selecteert u **publiceren**.

   ![Web-app publiceren in azure](./media/app-service-web-get-started-dotnet/publish-web-app-vs2019.png)

   Visual Studio publiceert uw ASP.NET Core web-app naar Azure en start de app in de standaard browser. 

   ![Gepubliceerde ASP.NET-Web-app die wordt uitgevoerd in azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

**Voltooid!** Uw ASP.NET Core web-app wordt live uitgevoerd in Azure App Service.

## <a name="update-the-app-and-redeploy"></a>De app bijwerken en opnieuw implementeren

Volg deze stappen om uw web-app bij te werken en opnieuw te implementeren:

1. Open in **Solution Explorer**onder uw project de **pagina's** > **index. cshtml**.

1. Vervang de volledige `<div>` tag door de volgende code:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Als u opnieuw wilt implementeren naar Azure, klikt u in **Solution Explorer** met de rechtermuisknop op het project **myFirstAzureWebApp** en selecteert u **Publiceren**.

1. Selecteer op de pagina samen vatting **publiceren** de optie **publiceren**.

   ![Update publiceren naar web-app](./media/app-service-web-get-started-dotnet/publish-update-to-web-app-vs2019.png)

Als het publiceren is voltooid, start Visual Studio een browser waarin de URL van de web-app wordt geladen.

![Bijgewerkte ASP.NET-Web-app die wordt uitgevoerd in azure](./media/app-service-web-get-started-dotnet/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>De Azure-app beheren

Als u uw web-app wilt beheren, gaat u naar de [Azure Portal](https://portal.azure.com)en zoekt en selecteert u **app Services**.

![App Services selecteren](./media/app-service-web-get-started-dotnet/app-services.png)

Selecteer op de pagina **app Services** de naam van uw web-app.

![Navigatie naar Azure-app in de portal](./media/app-service-web-get-started-dotnet/select-app-service.png)

De **overzichts** pagina voor uw web-app bevat opties voor het basis beheer zoals bladeren, stoppen, starten, opnieuw starten en verwijderen. In het linkermenu vindt u meer pagina's voor het configureren van uw app.

![App Service in Azure Portal](./media/app-service-web-get-started-dotnet/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u Visual Studio gebruikt om een ASP.NET Core web-app te maken en te implementeren op Azure App Service.

Ga naar het volgende artikel voor meer informatie over het maken van een .NET core-app en om deze te verbinden met een SQL Database:

> [!div class="nextstepaction"]
> [ASP.NET Core met SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
