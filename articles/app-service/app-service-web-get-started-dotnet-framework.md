---
title: 'Quick Start: een C# ASP.net-app maken'
description: Meer informatie over het uitvoeren van web-apps in Azure App Service door C# de standaard sjabloon voor ASP.net Web-apps te implementeren vanuit Visual Studio.
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: f2e31723dc2761007994f652d9d6c28ab3f7d47d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241484"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Een ASP.NET Framework-web-app maken in Azure

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie.

In deze snelstartgids ziet u hoe u uw eerste ASP.NET-web-app implementeert naar Azure App Service. Wanneer u klaar bent, hebt u een App Service plan. U hebt ook een App Service-app met een geïmplementeerde webtoepassing.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt volt ooien, installeert u <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> met de **ASP.net-en Web Development** -werk belasting.

Als u Visual Studio 2019 al hebt geïnstalleerd:

- Installeer de meest recente updates in Visual Studio door **Help** te selecteren > **op updates te controleren**.
- Voeg de werk belasting toe door **extra** > **Hulpprogram Ma's en functies ophalen**te selecteren.

## Een ASP.NET-Web-app maken<a name="create-and-publish-the-web-app"></a>

Maak een ASP.NET-Web-app door de volgende stappen uit te voeren:

1. Open Visual Studio en selecteer vervolgens **een nieuw project maken**.

2. Zoek in **een nieuw project maken**en kies **ASP.net Web Application (.NET Framework)** en selecteer vervolgens **volgende**.

3. Geef in **uw nieuwe project een**naam voor de toepassing _myFirstAzureWebApp_en selecteer vervolgens **maken**.

   ![Uw web-app-project configureren](./media/app-service-web-get-started-dotnet-framework/configure-web-app-project-framework.png)

4. U kunt elk type ASP.NET-web-app implementeren in Azure. Kies voor deze Quick Start de **MVC** -sjabloon.

5. Zorg ervoor dat verificatie is ingesteld op **geen verificatie**. Selecteer **Maken**.

   ![ASP.NET-webtoepassing maken](./media/app-service-web-get-started-dotnet-framework/select-mvc-template-vs2019.png)

6. Selecteer in het menu van Visual Studio **fout opsporing** > **zonder fout opsporing starten** om de web-app lokaal uit te voeren.

   ![De app lokaal uitvoeren](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## Uw web-app publiceren<a name="launch-the-publish-wizard"></a>

1. Klik in **Solution Explorer**met de rechter muisknop op het project **MyFirstAzureWebApp** en selecteer **publiceren**.

1. Kies **app service** en wijzig vervolgens **profiel maken** om te **publiceren**.

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
   |App Service-plan| myAppServicePlan | De naam van het App Service-plan. |
   | Locatie | Europa -west | Het datacenter waar de web-app wordt gehost. |
   | Grootte | Gratis | De [prijscategorie](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) bepaalt de hosting-functies. |

   ![Een App Service-plan maken](./media/app-service-web-get-started-dotnet-framework/app-service-plan-framework-vs2019.png)

1. Voer bij **naam**een unieke app-naam in die alleen de geldige tekens bevat `a-z`, `A-Z`, `0-9`en `-`. U kunt de automatisch gegenereerde unieke naam accepteren. De URL van de web-app is `http://<app_name>.azurewebsites.net`, waarbij `<app_name>` de naam van uw app is.

2. Selecteer **Maken** om de Azure-resources te gaan maken.

   ![Uw app-naam configureren](./media/app-service-web-get-started-dotnet-framework/web-app-name-framework-vs2019.png)

Zodra de wizard is voltooid, wordt de ASP.NET-web-app naar Azure gepubliceerd. Daarna wordt de app gestart in de standaardbrowser.

![Gepubliceerde ASP.NET-web-app in Azure](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

De naam van de app die is opgegeven in de **app service nieuwe pagina maken** , wordt gebruikt als het URL-voor voegsel in de notatie `http://<app_name>.azurewebsites.net`.

**Gefeliciteerd!** Uw ASP.NET-Web-app wordt live uitgevoerd in Azure App Service.

## <a name="update-the-app-and-redeploy"></a>De app bijwerken en opnieuw implementeren

1. Open in **Solution Explorer**onder uw project **weer gaven** > **Start** > **index. cshtml**.

1. Zoek ergens bovenaan de HTML-tag `<div class="jumbotron">` en vervang het volledige element door de volgende code:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
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

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [ASP.NET met SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
