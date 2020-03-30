---
title: 'Snelstart: een C#-ASP.NET-app maken'
description: Meer informatie over het uitvoeren van web-apps in Azure App Service door de standaard C#ASP.NET webapp-sjabloon vanuit Visual Studio te implementeren.
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 10/21/2019
ms.custom: mvc, devcenter, seodec18
ms.openlocfilehash: 4688cc358ec6ff792be58254b0607f5416422a21
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047660"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Een ASP.NET Framework-web-app maken in Azure

[Azure App Service](overview.md) biedt een zeer schaalbare, zelfpatchende webhostingservice.

In deze snelstartgids ziet u hoe u uw eerste ASP.NET-web-app implementeert naar Azure App Service. Wanneer u klaar bent, hebt u een App Service-abonnement. U hebt ook een App Service-app met een geïmplementeerde webtoepassing.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Installeer Visual Studio <a href="https://www.visualstudio.com/downloads/" target="_blank">2019</a> met de **ASP.NET en webdevelopment** workload om deze zelfstudie te voltooien.

Als u Visual Studio 2019 al hebt geïnstalleerd:

- Installeer de nieuwste updates in Visual Studio door > **Help-controle op updates te**selecteren. **Help**
- Voeg de werkbelasting toe door **Hulpmiddelen** > **en onderdelen op te**halen.

## <a name="create-an-aspnet-web-app"></a>Een ASP.NET web-app maken<a name="create-and-publish-the-web-app"></a>

Maak een ASP.NET web-app door de volgende stappen te volgen:

1. Open Visual Studio en selecteer **Vervolgens Een nieuw project maken**.

2. Selecteer in **Een nieuw project maken**ASP.NET **webtoepassing (.NET Framework)** en selecteer **Volgende**.

3. Geef in **Uw nieuwe project configureren**de naam van de toepassing _myFirstAzureWebApp_en selecteer **Vervolgens Maken**.

   ![Uw web-app-project configureren](./media/app-service-web-get-started-dotnet-framework/configure-web-app-project-framework.png)

4. U kunt elk type ASP.NET-web-app implementeren in Azure. Kies voor deze quickstart de **MVC-sjabloon.**

5. Controleer of verificatie is ingesteld op **Geen verificatie**. Selecteer **Maken**.

   ![ASP.NET webtoepassing maken](./media/app-service-web-get-started-dotnet-framework/select-mvc-template-vs2019.png)

6. Selecteer in het menu Visual Studio de optie **Foutopsporing** > **zonder foutopsporing** om de web-app lokaal uit te voeren.

   ![De app lokaal uitvoeren](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## <a name="publish-your-web-app"></a>Uw web-app publiceren<a name="launch-the-publish-wizard"></a>

1. Klik in **Solution Explorer**met de rechtermuisknop op het **myFirstAzureWebApp-project** en selecteer **Publiceren**.

1. Kies **App-service** en wijzig **vervolgens Profiel maken** om te **publiceren**.

   ![Publiceren vanaf de projectoverzichtspagina](./media/app-service-web-get-started-dotnet-framework/publish-app-framework-vs2019.png)

1. In **App Service Nieuw maken,** zijn uw opties afhankelijk van of u al bent aangemeld bij Azure en of u een Visual Studio-account hebt dat is gekoppeld aan een Azure-account. Selecteer **Een account toevoegen** of **Aanmelden** om u aan te melden bij uw Azure-abonnement. Als u al bent aangemeld, selecteert u het gewenste account.

   > [!NOTE]
   > Als u al bent aangemeld, selecteert u **Maken** nog niet.
   >
   >

   ![Aanmelden bij Azure](./media/app-service-web-get-started-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. **Selecteer**Nieuw voor **resourcegroep**.

1. Voer in **De naam van de brongroep nieuw** *myResourceGroup* in en selecteer **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Selecteer **Nieuw**voor **hostingplan**.

1. Voer in het dialoogvenster **Hostingplan configureren** de waarden uit de volgende tabel in en selecteer **OK**.

   | Instelling | Voorgestelde waarde | Beschrijving |
   |-|-|-|
   |App Service-plan| myAppServicePlan | De naam van het App Service-plan. |
   | Locatie | Europa -west | Het datacenter waar de web-app wordt gehost. |
   | Grootte | Gratis | De [prijscategorie](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) bepaalt de hosting-functies. |

   ![Een App Service-plan maken](./media/app-service-web-get-started-dotnet-framework/app-service-plan-framework-vs2019.png)

1. Voer in **Naam**een unieke app-naam in `a-z` `A-Z`die `0-9`alleen `-`de geldige tekens bevat, en . U de automatisch gegenereerde unieke naam accepteren. De URL van de web-app is `http://<app_name>.azurewebsites.net`, waarbij `<app_name>` de naam van uw app is.

2. Selecteer **Maken** om de Azure-resources te gaan maken.

   ![Uw app-naam configureren](./media/app-service-web-get-started-dotnet-framework/web-app-name-framework-vs2019.png)

Zodra de wizard is voltooid, wordt de ASP.NET-web-app naar Azure gepubliceerd. Daarna wordt de app gestart in de standaardbrowser.

![Gepubliceerde ASP.NET-web-app in Azure](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

De app-naam die is opgegeven op de pagina Nieuwe pagina `http://<app_name>.azurewebsites.net`Maken van **app-service,** wordt gebruikt als het URL-voorvoegsel in de indeling .

**Gefeliciteerd!** Uw web-app ASP.NET wordt live uitgevoerd in Azure App Service.

## <a name="update-the-app-and-redeploy"></a>De app bijwerken en opnieuw implementeren

1. Open **In Solution Explorer**onder uw project **Views** > **Home** > **Index.cshtml**.

1. Zoek ergens bovenaan de HTML-tag `<div class="jumbotron">` en vervang het volledige element door de volgende code:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Als u opnieuw wilt implementeren naar Azure, klikt u in **Solution Explorer** met de rechtermuisknop op het project **myFirstAzureWebApp** en selecteert u **Publiceren**. Selecteer vervolgens **Publiceren**.

Als het publiceren is voltooid, start Visual Studio een browser waarin de URL van de web-app wordt geladen.

![Bijgewerkte ASP.NET-web-app in Azure](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>De Azure-app beheren

1. Als u de web-app wilt beheren, gaat u naar de [Azure-portal](https://portal.azure.com)en zoekt en selecteert u **App Services**.

   ![App-services selecteren](./media/app-service-web-get-started-dotnet-framework/app-services.png)

2. Selecteer op de pagina **App Services** de naam van uw web-app.

   ![Navigatie naar Azure-app in de portal](./media/app-service-web-get-started-dotnet-framework/access-portal-framework-vs2019.png)

   De pagina Overzicht van uw web-app wordt weergegeven. Hier u basisbeheer doen, zoals bladeren, stoppen, starten, opnieuw starten en verwijderen.

   ![Overzicht van App-service in Azure-portal](./media/app-service-web-get-started-dotnet-framework/web-app-general-framework-vs2019.png)

   Het linkermenu bevat een aantal pagina's voor het configureren van uw app.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [ASP.NET met SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
