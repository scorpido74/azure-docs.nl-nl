---
title: 'Quickstart: Een C# ASP.NET Core-app maken'
description: Leer hoe u web-apps uitvoert in Azure App Service door uw eerste ASP.NET Core-app te implementeren.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 95f877c9030d51e68b97a21875ff3ff05b383457
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90973765"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Quickstart: Een ASP.NET Core-web-app maken in Azure

::: zone pivot="platform-windows"  

In deze quickstart leert u hoe u uw eerste ASP.NET Core-web-app maakt en implementeert in [Azure App Service](overview.md). 

Als u klaar bent, beschikt u over een Azure-resourcegroep die bestaat uit een App Service-hostingabonnement en een Azure Service-app met een geïmplementeerde webtoepassing.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/dotnet/)
- In deze quickstart implementeert u een app in App Service onder Windows. Als u een app wilt implementeren in App Service onder _Linux_, raadpleegt u [Een .NET Core-web-app maken in App Service in Linux](./quickstart-dotnetcore.md).
- U moet <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> met de **ASP.NET- en webontwikkelworkload** installeren.

  Als u Visual Studio 2019 al hebt geïnstalleerd:

  - Installeer de nieuwste updates in Visual Studio door **Help** > **Controleren op updates** te selecteren.
  - Voeg de workload toe door **Hulpprogramma's** > **Hulpprogramma's en functies ophalen** te selecteren.


## <a name="create-an-aspnet-core-web-app"></a>Een ASP.NET Core-web-app maken

Maak een ASP.NET Core-web-app in Visual Studio door deze stappen te volgen:

1. Open Visual Studio en selecteer **Een nieuw project maken**.

1. Selecteer in **Een nieuw project maken** de **ASP.NET Core-webtoepassing**, en controleer of **C#** wordt vermeld in de talen voor deze keuze. Selecteer vervolgens **Volgende**.

1. Geef in **Uw nieuwe project configureren** het webtoepassingsproject de naam *myFirstAzureWebApp*, en selecteer **Maken**.

   ![Uw web-app-project configureren](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. U kunt elk type ASP.NET Core-web-app implementeren in Azure, maar voor deze quickstart kiest u de sjabloon **Webtoepassing**. Zorg ervoor dat **Verificatie** is ingesteld op **Geen verificatie** en dat er geen andere optie is geselecteerd. Ten slotte selecteert u **Create**.

   ![Een nieuw ASP.NET Core-web-app maken](./media/quickstart-dotnetcore/create-aspnet-core-web-app.png) 
   
1. Selecteer in het menu van Visual Studio de optie **Foutopsporing** > **Starten zonder foutopsporing** om de web-app lokaal uit te voeren.

   ![Web-app die lokaal wordt uitgevoerd](./media/quickstart-dotnetcore/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Uw web-app publiceren

Als u de web-app wilt publiceren, moet u eerst een nieuwe App Service maken en configureren waarin u de app kunt publiceren. 

Als onderdeel van het instellen van de App Service maakt u het volgende:

- Een nieuwe [resource groep](../azure-resource-manager/management/overview.md#terminology) die alle Azure-resources voor de service bevat.
- Een nieuw [Hostingabonnement](./overview-hosting-plans.md) dat de locatie, grootte en functies opgeeft van de webserverfarm die als host fungeert voor de app.

Volg deze stappen om de App Service te maken en uw web-app te publiceren:

1. Klik in **Solution Explorer** met de rechtermuisknop op het project **myFirstAzureWebApp** en selecteer **Publiceren**. Als u zich nog niet hebt aangemeld bij uw Azure-account vanuit Visual Studio, selecteert u **Een account toevoegen** of **Aanmelden**. U kunt ook een gratis Azure-account maken.

1. Kies in het dialoogvenster **Een publicatiedoel kiezen** de optie **App Service**, selecteer **Nieuwe maken** en selecteer vervolgens **Profiel maken**.

   ![Kies een publicatiedoel](./media/quickstart-dotnetcore/pick-publish-target-vs2019.png)

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
   
   ![Nieuw hostingabonnement maken](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. Houd **Application Insights** ingesteld op *Geen*.

1. Selecteer in het dialoogvenster **App Service: Nieuwe App Service maken** de optie **Maken** om te beginnen met het maken van de Azure-resources.

   ![Nieuwe App Service maken](./media/quickstart-dotnetcore/create-new-app-service-vs2019.png)

1. Zodra de wizard is voltooid, selecteert u **Publiceren**.

   ![Web-app publiceren in Azure](./media/quickstart-dotnetcore/publish-web-app-vs2019.png)

   Met Visual Studio wordt uw ASP.NET Core web-app gepubliceerd in Azure. De app wordt gestart in de standaardbrowser. 

   ![Gepubliceerde ASP.NET-web-app, uitgevoerd in Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

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

   ![Update voor web-app publiceren](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png)

Als het publiceren is voltooid, start Visual Studio een browser waarin de URL van de web-app wordt geladen.

![Bijgewerkte ASP.NET-web-app, uitgevoerd in Azure](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>De Azure-app beheren

Als u de web-app wilt beheren, gaat u naar de [Azure-portal](https://portal.azure.com) en selecteert u **App Services**.

![App Services selecteren](./media/quickstart-dotnetcore/app-services.png)

Selecteer op de pagina **App Services** de naam van uw web-app.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Schermopname van de App Services-pagina waarop een voorbeeldweb-app is geselecteerd.":::

De **Overzichtspagina** voor de web-app bevat opties voor basisbeheer, zoals browsen, stoppen, starten, opnieuw starten en verwijderen. Het linkermenu bevat een meer pagina's voor het configureren van uw app.

![App Service in de Azure-portal](./media/quickstart-dotnetcore/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u Visual Studio gebruikt om een ASP.NET Core-webtoepassing te maken en implementeren in Azure App Service.

Ga door met het volgende artikel om te leren hoe u een .NET Core-app maakt en deze verbindt met een SQL-database:

> [!div class="nextstepaction"]
> [ASP.NET Core met SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core-app configureren](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
[App Service onder Linux](overview.md#app-service-on-linux) biedt een uiterst schaalbare webhostingservice met self-patchfunctie onder het Linux-besturingssysteem. Deze quickstart laat zien hoe u een [.NET Core](/aspnet/core/)-app maakt in App Service on Linux. U maakt de app via de [Azure CLI](/cli/azure/get-started-with-azure-cli) en gebruikt Git om de .NET Core-code in de app te implementeren.

![Voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

U kunt de stappen in dit artikel volgen met behulp van een Mac-, Windows- of Linux-computer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

* <a href="https://git-scm.com/" target="_blank">Git installeren</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">De nieuwste versie van .NET Core 3.1 SDK installeren</a>

## <a name="create-the-app-locally"></a>De app lokaal maken

Maak in een terminalvenster op uw computer een map met de naam `hellodotnetcore` en wijzig de huidige map in die map.

```bash
mkdir hellodotnetcore
cd hellodotnetcore
```

Een nieuwe .NET Core-app maken

```bash
dotnet new web
```

## <a name="run-the-app-locally"></a>De app lokaal uitvoeren

Voer de toepassing lokaal uit zodat u kunt zien hoe deze eruit ziet wanneer u de toepassing implementeert naar Azure. 

Herstel de NuGet-pakketten en voer de app uit.

```bash
dotnet run
```

Open een webbrowser en navigeer naar de app op `http://localhost:5000`.

Het bericht **Hello World** uit de voorbeeld-app wordt weergegeven op de pagina.

![Testen met browser](media/quickstart-dotnetcore/dotnet-browse-local.png)

Druk in uw terminalvenster op **Ctrl + C** om de webserver af te sluiten. Initialiseer een Git-opslagplaats voor het .NET Core-project.

```bash
git init
git add .
git commit -m "first commit"
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Een webtoepassing maken

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

Blader naar de app die u net hebt gemaakt. Vervang _&lt;app-naam>_ door de naam van uw app.

```bash
https://<app-name>.azurewebsites.net
```

Uw nieuwe app lijkt op het volgende:

![Lege app-pagina](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net:443/&lt;app-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

## <a name="browse-to-the-app"></a>Bladeren naar de app

Blader naar de geïmplementeerde toepassing via uw webbrowser.

```bash
http://<app_name>.azurewebsites.net
```

De .NET Core-voorbeeldcode wordt uitgevoerd in App Service op Linux met een ingebouwde installatiekopie.

![Voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

**Gefeliciteerd!** U hebt uw eerste .NET Core-app geïmplementeerd in Azure App Service on Linux.

## <a name="update-and-redeploy-the-code"></a>De code bijwerken en opnieuw implementeren

Open het bestand _Startup.cs_ in de lokale map. Breng een kleine wijziging aan in de tekst in de methodeaanroep `context.Response.WriteAsync`:

```csharp
await context.Response.WriteAsync("Hello Azure!");
```

Leg uw wijzigingen vast in Git en push de codewijzigingen vervolgens naar Azure.

```bash
git commit -am "updated output"
git push azure master
```

Als de implementatie is voltooid, gaat u terug naar het browservenster dat is geopend in de stap **Bladeren naar de app** en klikt u op Vernieuwen.

![Bijgewerkte voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-app"></a>Uw nieuwe Azure-app beheren

Ga naar <a href="https://portal.azure.com" target="_blank">Azure Portal</a> om de app te beheren die u hebt gemaakt.

Klik in het linkermenu op **App Services** en klik op de naam van uw Azure-app.

:::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list.png" alt-text="Schermopname van de App Services-pagina waarop een voorbeeldweb-app is geselecteerd.":::

De pagina Overzicht van uw app wordt weergegeven. Hier kunt u algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen. 

![App Service-pagina in Azure Portal](media/quickstart-dotnetcore/portal-app-overview.png)

Het linkermenu bevat een aantal pagina's voor het configureren van uw app. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: ASP.NET Core-app met SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core-app configureren](configure-language-dotnetcore.md)

::: zone-end