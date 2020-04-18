---
title: Profiel ASP.NET Core Azure Linux-webapps met Application Insights Profiler | Microsoft Documenten
description: Een conceptueel overzicht en stapsgewijze zelfstudie over het gebruik van Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 02/23/2018
ms.reviewer: mbullwin
ms.openlocfilehash: d845e245a242a88d16a2597f0144a0ae4a727cb0
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640979"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profiel ASP.NET Core Azure Linux-webapps met Application Insights Profiler

Deze functie is momenteel beschikbaar als preview-product.

Ontdek hoeveel tijd er wordt besteed aan elke methode van uw live webapplicatie bij het gebruik van [Application Insights.](../../azure-monitor/app/app-insights-overview.md) Application Insights Profiler is nu beschikbaar voor ASP.NET Core-webapps die worden gehost in Linux op Azure App Service. Deze handleiding geeft stapsgewijze instructies over hoe de Profiler-sporen kunnen worden verzameld voor ASP.NET Core Linux-webapps.

Nadat u deze walkthrough hebt voltooid, kan uw app Profiler-sporen verzamelen, zoals de sporen die in de afbeelding worden weergegeven. In dit voorbeeld geeft de profilertracering aan dat een bepaalde webaanvraag traag is vanwege de wachttijd. Het *hete pad* in de code die de app vertraagt, wordt gemarkeerd door een vlampictogram. De **methode Over** in de sectie **HomeController** vertraagt de web-app omdat de methode de **thread.sleep-functie** aanroept.

![Profiel van profiler](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Vereisten
De volgende instructies zijn van toepassing op alle ontwikkelomgevingen voor Windows, Linux en Mac:

* Installeer de [.NET Core SDK 2.1.2 of hoger](https://dotnet.microsoft.com/download/archives).
* Installeer Git door de instructies te volgen bij [Aan de slag - Git installeren](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Het project lokaal instellen

1. Open een opdrachtpromptvenster op uw machine. De volgende instructies werken voor alle ontwikkelomgevingen voor Windows, Linux en Mac.

1. Maak een ASP.NET Core MVC-webtoepassing:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

1. Wijzig de werkmap in de hoofdmap voor het project.

1. Voeg het NuGet-pakket toe om de Profiler-sporen te verzamelen:

    ```shell
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

1. Toepassingsinzichten inschakelen in Program.cs:

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```
    
1. Profiler inschakelen in Startup.cs:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Voeg een coderegel toe in de **sectie HomeController.cs** om willekeurig een paar seconden uit te stellen:

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```

1. Sla uw wijzigingen op en verbind deze in de lokale opslagplaats:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>De Linux-web-app maken om uw project te hosten

1. Maak de web-app-omgeving met App Service op Linux:

    ![De Linux-web-app maken](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Maak de implementatiereferenties:

    > [!NOTE]
    > Neem uw wachtwoord op om later te gebruiken bij het implementeren van uw web-app.

    ![De implementatiereferenties maken](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Kies de implementatieopties. Stel een lokale Git-opslagplaats in de web-app in door de instructies op de Azure-portal te volgen. Er wordt automatisch een Git-repository gemaakt.

    ![De Git-opslagplaats instellen](./media/profiler-aspnetcore-linux/setup-git-repo.png)

Zie [dit artikel](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type)voor meer implementatieopties.

## <a name="deploy-your-project"></a>Uw project implementeren

1. Blader in het venster Opdrachtprompt naar de hoofdmap voor uw project. Voeg een Git remote repository toe om naar de repository op App Service te wijzen:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Gebruik de **gebruikersnaam** die u hebt gebruikt om de implementatiereferenties te maken.
    * Gebruik de **app-naam** die u hebt gebruikt om de web-app te maken met behulp van App Service op Linux.

2. Implementeer het project door de wijzigingen in Azure te pushen:

    ```
    git push azure master
    ```

U ziet uitvoer vergelijkbaar met het volgende voorbeeld:

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Toepassingsinzichten toevoegen om uw web-apps te controleren

1. [Een application insights-bron maken.](./../../azure-monitor/app/create-new-resource.md )

2. Kopieer de **iKey-waarde** van de application insights-bron en stel de volgende instellingen in uw web-apps in:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ```

    Wanneer de app-instellingen worden gewijzigd, wordt de site automatisch opnieuw opgestart. Nadat de nieuwe instellingen zijn toegepast, loopt de Profiler onmiddellijk twee minuten. De Profiler loopt dan twee minuten per uur.

3. Genereer wat verkeer naar uw website. U verkeer genereren door de site **Over-pagina** een paar keer te vernieuwen.

4. Wacht twee tot vijf minuten voordat de gebeurtenissen worden samengevoegd tot Application Insights.

5. Blader naar het **prestatievenster voor** toepassingsinzichten in de Azure-portal. U de Sporen van profiler bij de bodem van het venster bekijken.

    ![Profiel van Profiler weergeven](./media/profiler-aspnetcore-linux/view-traces.png)



## <a name="next-steps"></a>Volgende stappen
Als u aangepaste containers gebruikt die worden gehost door Azure App Service, volgt u de instructies in [Service profiler inschakelen voor een containerized ASP.NET Core-toepassing](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) om Application Insights Profiler in te schakelen.

Eventuele problemen of suggesties melden aan de Application Insights GitHub repository: [ApplicationInsights-Profiler-AspNetCore: Issues](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
