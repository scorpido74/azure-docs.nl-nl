---
title: 'Snelstartgids: een C# ASP.NET-app maken'
description: Meer informatie over het uitvoeren van web-apps in Azure App Service door de standaard sjabloon C# ASP.NET Web app te implementeren vanuit Visual Studio.
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 04/21/2020
ms.custom: mvc, devcenter, seodec18
ms.openlocfilehash: 056c96807fed7d5acca85635440c8fe4d989c933
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592385"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Een ASP.NET Framework-web-app maken in Azure

[Azure app service](overview.md) biedt een uiterst schaal bare webhostingservice met self-patch functie.

In deze snelstartgids ziet u hoe u uw eerste ASP.NET-web-app implementeert naar Azure App Service. Wanneer u klaar bent, hebt u een App Service plan. U hebt ook een App Service-app met een geïmplementeerde webtoepassing.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt volt ooien, installeert u <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> met de **ASP.net-en Web Development** -werk belasting.

Als u Visual Studio 2019 al hebt geïnstalleerd:

- Installeer de meest recente updates in Visual Studio door **Help** > **controleren op updates**te selecteren.
- Voeg de werk belasting toe door **extra** > hulp middelen**en functies**te selecteren.

## <a name="create-an-aspnet-web-app"></a>Een ASP.NET-Web-app maken<a name="create-and-publish-the-web-app"></a>

Maak een ASP.NET-Web-app door de volgende stappen uit te voeren:

1. Open Visual Studio en selecteer vervolgens **een nieuw project maken**.

2. Zoek in **een nieuw project maken**en kies **ASP.net Web Application (.NET Framework)** en selecteer vervolgens **volgende**.

3. Geef in **uw nieuwe project een**naam voor de toepassing _myFirstAzureWebApp_en selecteer vervolgens **maken**.

   ![Uw web-app-project configureren](./media/app-service-web-get-started-dotnet-framework/configure-web-app-project-framework.png)

4. U kunt elk type ASP.NET-web-app implementeren in Azure. Kies voor deze Quick Start de **MVC** -sjabloon.

5. Zorg ervoor dat verificatie is ingesteld op **geen verificatie**. Selecteer **Maken**.

   ![ASP.NET-webtoepassing maken](./media/app-service-web-get-started-dotnet-framework/select-mvc-template-vs2019.png)

6. Selecteer in het Visual Studio-menu de optie **fout opsporing** > **starten zonder fout opsporing** om de web-app lokaal uit te voeren.

   ![De app lokaal uitvoeren](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## <a name="publish-your-web-app"></a>Uw web-app publiceren<a name="launch-the-publish-wizard"></a>

1. Klik in **Solution Explorer**met de rechter muisknop op het project **MyFirstAzureWebApp** en selecteer **publiceren**.

1. Kies **app service** en selecteer **profiel maken**.

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

   | Instelling | Voorgestelde waarde | Beschrijving |
   |-|-|-|
   | Hostingabonnement| myAppServicePlan | De naam van het App Service-plan. |
   | Locatie | Europa -west | Het datacenter waar de web-app wordt gehost. |
   | Grootte | Gratis | De [prijscategorie](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) bepaalt de hosting-functies. |

   ![Een App Service-plan maken](./media/app-service-web-get-started-dotnet-framework/app-service-plan-framework-vs2019.png)

1. Voer bij **naam**een unieke app-naam in die alleen de geldige tekens bevat `a-z`: `A-Z` `0-9`,, en `-`. U kunt de automatisch gegenereerde unieke naam accepteren. De URL van de web-app is `http://<app-name>.azurewebsites.net`, waarbij `<app-name>` de naam van uw app is.

2. Selecteer **Maken** om de Azure-resources te gaan maken.

   ![Uw app-naam configureren](./media/app-service-web-get-started-dotnet-framework/web-app-name-framework-vs2019.png)

    Zodra de wizard is voltooid, worden de Azure-resources voor u gemaakt en bent u klaar om te publiceren.

3. Klik op de pagina **publiceren** op **publiceren**. Visual Studio bouwt, verpakt en publiceert de app naar Azure, waarna de app wordt gestart in de standaard browser.

    ![Gepubliceerde ASP.NET-web-app in Azure](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

De naam van de app die is opgegeven in de **app service nieuwe pagina maken** , wordt gebruikt als het `http://<app-name>.azurewebsites.net`URL-voor voegsel in de notatie.

**Voltooid!** Uw ASP.NET-Web-app wordt live uitgevoerd in Azure App Service.

## <a name="update-the-app-and-redeploy"></a>De app bijwerken en opnieuw implementeren

1. Open in **Solution Explorer** **weer gaven** > **Start** > **index. cshtml**in het project.

1. Zoek ergens bovenaan de HTML-tag `<div class="jumbotron">` en vervang het volledige element door de volgende code:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Als u opnieuw wilt implementeren naar Azure, klikt u in **Solution Explorer** met de rechtermuisknop op het project **myFirstAzureWebApp** en selecteert u **Publiceren**. Selecteer vervolgens **publiceren**.

    Als het publiceren is voltooid, start Visual Studio een browser waarin de URL van de web-app wordt geladen.

    ![Bijgewerkte ASP.NET-web-app in Azure](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>De Azure-app beheren

1. Als u de Web-App wilt beheren, gaat u naar de [Azure Portal](https://portal.azure.com)en zoekt en selecteert u **app Services**.

   ![App-services selecteren](./media/app-service-web-get-started-dotnet-framework/app-services.png)

2. Selecteer op de pagina **app Services** de naam van uw web-app.

   ![Navigatie naar Azure-app in de portal](./media/app-service-web-get-started-dotnet-framework/access-portal-framework-vs2019.png)

   De pagina Overzicht van uw web-app wordt weergegeven. Hier kunt u het basis beheer, zoals bladeren, stoppen, starten, opnieuw starten en verwijderen.

   ![App Service overzicht in Azure Portal](./media/app-service-web-get-started-dotnet-framework/web-app-general-framework-vs2019.png)

   Het linkermenu bevat een aantal pagina's voor het configureren van uw app.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [ASP.NET met SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
