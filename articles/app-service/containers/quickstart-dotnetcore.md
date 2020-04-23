---
title: 'Snelstart: Een Linux-ASP.NET Core-app uitvoeren'
description: Ga aan de slag met Linux-apps in Azure App Service door uw eerste ASP.NET Core-app te implementeren in een Linux-container in App Service.
keywords: azure app service, web-app, dotnet, core, linux, oss
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.tgt_pltfrm: linux
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 349aa4652ae9f52641b5865e48dcd4c113ca7f9d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086284"
---
# <a name="create-an-aspnet-core-app-in-app-service-on-linux"></a>Een ASP.NET Core-app maken in App Service op Linux

> [!NOTE]
> In dit artikel gaat u een app implementeren in App Service onder Linux. Zie [Een ASP.NET Core-app maken in Azure](../app-service-web-get-started-dotnet.md) om een app te implementeren in App Service op _Windows_.
>

[Azure App Service on Linux](app-service-linux-intro.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie onder het Linux-besturingssysteem. Deze quickstart laat zien hoe u een [.NET Core](https://docs.microsoft.com/aspnet/core/)-app maakt in App Service on Linux. U maakt de app via de [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) en gebruikt Git om de .NET Core-code in de app te implementeren.

![Voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

U kunt de stappen in dit artikel volgen met behulp van een Mac-, Windows- of Linux-computer.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

* <a href="https://git-scm.com/" target="_blank">Git installeren</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Installeer de nieuwste .NET Core SDK 3.1</a>

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

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Een webtoepassing maken

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

Blader naar de app die u net hebt gemaakt. Vervang _ &lt;de app-naam>_ door de naam van uw app.

```bash
https://<app-name>.azurewebsites.net
```

Uw nieuwe app lijkt op het volgende:

![Lege app-pagina](media/quickstart-dotnetcore/dotnet-browse-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

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

Zodra de implementatie is voltooid, schakelt u terug naar het browservenster dat is geopend in de stap **Bladeren naar de app** en druk je op Vernieuwen.

![Bijgewerkte voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)

## <a name="manage-your-new-azure-app"></a>Uw nieuwe Azure-app beheren

Ga naar <a href="https://portal.azure.com" target="_blank">Azure Portal</a> om de app te beheren die u hebt gemaakt.

Klik in het linkermenu op **App Services** en klik op de naam van uw Azure-app.

![Navigatie naar Azure-app in de portal](./media/quickstart-dotnetcore/portal-app-service-list.png)

De pagina Overzicht van uw app wordt weergegeven. Hier kunt u algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen. 

![App Service-pagina in Azure Portal](media/quickstart-dotnetcore/portal-app-overview.png)

Het linkermenu bevat een aantal pagina's voor het configureren van uw app. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: ASP.NET Core-app met SQL-database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core-app configureren](configure-language-dotnetcore.md)
