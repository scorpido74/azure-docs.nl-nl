---
title: 'Snelstart: een C# ASP.NET Core-app maken'
description: Meer informatie over het uitvoeren van web-apps in Azure App Service door de standaardC#ASP.NET Core-webappsjabloon vanuit Visual Studio te implementeren.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 6c7ab1290033792cf356c4882811670011df5efe
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086042"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Snelstart: een ASP.NET Core-web-app maken in Azure

In deze snelle start leert u hoe u uw eerste ASP.NET Core-webapp maken en implementeren in [Azure App Service.](overview.md) 

Wanneer u klaar bent, beschikt u over een Azure-brongroep die bestaat uit een App Service-hostingplan en een App-service met een geïmplementeerde webtoepassing.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/dotnet/)
- Met deze quickstart wordt een app geïmplementeerd voor App Service op Windows. Zie [Een .NET Core-web-app maken en implementeren in App Service onder Linux ](./containers/quickstart-dotnetcore.md) om een app te implementeren in App Service onder _Linux_.
- Installeer <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> met de **ASP.NET en webdevelopment** workload.

  Als u Visual Studio 2019 al hebt geïnstalleerd:

  - Installeer de nieuwste updates in Visual Studio door > **Help-controle op updates te**selecteren. **Help**
  - Voeg de werkbelasting toe door **Hulpmiddelen** > **en onderdelen op te**halen.


## <a name="create-an-aspnet-core-web-app"></a>Een ASP.NET Core-web-app maken

Maak een ASP.NET Core-web-app in Visual Studio door de volgende stappen te volgen:

1. Open Visual Studio en selecteer **Een nieuw project maken**.

1. Selecteer **in Een nieuw project maken**ASP.NET **Kernwebtoepassing** en controleer of **C#** wordt weergegeven in de talen voor die keuze en selecteer **Volgende**.

1. Geef **in Uw nieuwe project configureren**een naam aan uw webtoepassingsproject *myFirstAzureWebApp*en selecteer **Maken**.

   ![Uw web-app-project configureren](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. U elk type ASP.NET Core-webapp implementeren naar Azure, maar kies voor deze snelle start de sjabloon **Webtoepassing.** Controleer of **Verificatie** is ingesteld op **Geen verificatie**en dat er geen andere optie is geselecteerd. Ten slotte selecteert u **Create**.

   ![Een nieuwe ASP.NET Core-web-app maken](./media/app-service-web-get-started-dotnet/create-aspnet-core-web-app.png) 
   
1. Selecteer in het menu Visual Studio de optie **Foutopsporing** > **zonder foutopsporing** om uw web-app lokaal uit te voeren.

   ![Web-app lokaal uitgevoerd](./media/app-service-web-get-started-dotnet/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Uw web-app publiceren

Als u uw web-app wilt publiceren, moet u eerst een nieuwe app-service maken en configureren waarop u uw app publiceren. 

Als onderdeel van het instellen van de App-service maakt u het:

- Een nieuwe [brongroep](https://docs.microsoft.com/azure/azure-resource-manager/management/overview#terminology) met alle Azure-bronnen voor de service.
- Een nieuw [hostingplan](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) dat de locatie, grootte en functies van de webserverfarm aangeeft die uw app host.

Volg de volgende stappen om uw App-service te maken en uw web-app te publiceren:

1. Klik in **Solution Explorer**met de rechtermuisknop op het **myFirstAzureWebApp-project** en selecteer **Publiceren**. Als u zich nog niet hebt aangemeld bij uw Azure-account vanuit Visual Studio, selecteert u **Een account toevoegen** of **Aanmelden.** U ook een gratis Azure-account maken.

1. Kies **app-service,** selecteer **Nieuw maken**en selecteer Vervolgens **Profiel maken**in het dialoogvenster **Een publicatiedoel** kiezen .

   ![Kies een publicatiedoel](./media/app-service-web-get-started-dotnet/pick-publish-target-vs2019.png)

1. In de **app-service: maak een nieuw** dialoogvenster en geef een wereldwijd unieke **naam** voor uw app op door de standaardnaam te accepteren of een nieuwe naam in te voeren. Geldige tekens `a-z`zijn: `0-9`, `-` `A-Z`, en . Deze **naam** wordt gebruikt als url-voorvoegsel `http://<app_name>.azurewebsites.net`voor uw web-app in de indeling.

1. Accepteer **bij Abonnement**het aangeboden abonnement of selecteer een nieuw abonnement in de vervolgkeuzelijst.

1. Selecteer **In de groep Resource**de optie **Nieuw**. Voer in **De naam van de brongroep nieuw** *myResourceGroup* in en selecteer **OK**. 

1. Selecteer **Nieuw** **voor hostingplan**. 

1. Voer in het **hostingplan: maak een nieuw** dialoogvenster en voer de waarden in die in de volgende tabel zijn opgegeven:

   | Instelling  | Voorgestelde waarde | Beschrijving |
   | -------- | --------------- | ----------- |
   | **Hostingabonnement**  | *myFirstAzureWebAppPlan* | De naam van het App Service-plan. |
   | **Locatie**      | *Europa -west* | Het datacenter waar de web-app wordt gehost. |
   | **Grootte**          | *Gratis* | De [prijscategorie](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) bepaalt de hosting-functies. |
   
   ![Nieuw hostingplan maken](./media/app-service-web-get-started-dotnet/create-new-hosting-plan-vs2019.png)

1. **Toepassingsinzichten laten** instellen op *Geen*.

1. Selecteer in het **dialoogvenster App-service: maak een** nieuw dialoogvenster en selecteer **Maken** om de Azure-resources te maken.

   ![Nieuwe app-service maken](./media/app-service-web-get-started-dotnet/create-new-app-service-vs2019.png)

1. Zodra de wizard is voltooid, selecteert u **Publiceren**.

   ![Web-app publiceren naar Azure](./media/app-service-web-get-started-dotnet/publish-web-app-vs2019.png)

   Visual Studio publiceert uw ASP.NET Core-web-app naar Azure en start de app in uw standaardbrowser. 

   ![Gepubliceerde web-app ASP.NET die wordt uitgevoerd in Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

**Gefeliciteerd!** Uw web-app ASP.NET Core wordt live uitgevoerd in Azure App Service.

## <a name="update-the-app-and-redeploy"></a>De app bijwerken en opnieuw implementeren

Volg deze stappen om uw web-app bij te werken en opnieuw te implementeren:

1. Open **Pages** > **Index.cshtml**in **Solution Explorer**onder uw project .

1. Vervang de `<div>` volledige tag door de volgende code:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Als u opnieuw wilt implementeren naar Azure, klikt u in **Solution Explorer** met de rechtermuisknop op het project **myFirstAzureWebApp** en selecteert u **Publiceren**.

1. Selecteer **Publiceren** op de pagina Overzicht **publiceren**.

   ![Update naar web-app publiceren](./media/app-service-web-get-started-dotnet/publish-update-to-web-app-vs2019.png)

Als het publiceren is voltooid, start Visual Studio een browser waarin de URL van de web-app wordt geladen.

![Bijgewerkte ASP.NET web-app die wordt uitgevoerd in Azure](./media/app-service-web-get-started-dotnet/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>De Azure-app beheren

Als u uw web-app wilt beheren, gaat u naar de [Azure-portal](https://portal.azure.com)en zoekt en selecteert u **App Services**.

![App-services selecteren](./media/app-service-web-get-started-dotnet/app-services.png)

Selecteer op de pagina **App Services** de naam van uw web-app.

![Navigatie naar Azure-app in de portal](./media/app-service-web-get-started-dotnet/select-app-service.png)

De **overzichtspagina** voor uw web-app bevat opties voor basisbeheer, zoals bladeren, stoppen, starten, opnieuw starten en verwijderen. Het linkermenu bevat verdere pagina's voor het configureren van uw app.

![App-service in Azure-portal](./media/app-service-web-get-started-dotnet/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u Visual Studio gebruikt om een ASP.NET Core-webapp te maken en te implementeren in Azure App Service.

Ga naar het volgende artikel voor meer informatie over het maken van een .NET Core-app en deze verbinden met een SQL-database:

> [!div class="nextstepaction"]
> [ASP.NET Core met SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
