---
title: Azure-appconfiguratie integreren met behulp van een pijplijn voor continue integratie en levering
description: Meer informatie over het implementeren van continue integratie en levering met Azure App-configuratie
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: lcozzens
ms.openlocfilehash: c744557471a9b37bd620bb9195bdb709c24649ab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77047290"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integreren met een CI/CD-pijplijn

In dit artikel wordt uitgelegd hoe u gegevens uit Azure App-configuratie gebruiken in een continu integratie- en continuimplementatiesysteem.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>App-configuratie gebruiken in uw Azure DevOps-pijplijn

Als u een Azure DevOps-pijplijn hebt, u belangrijke waarden ophalen uit app-configuratie en deze instellen als taakvariabelen. De [Azure App Configuration DevOps-extensie](https://go.microsoft.com/fwlink/?linkid=2091063) is een add-onmodule die deze functionaliteit biedt. Volg de instructies om de extensie te gebruiken in een build- of releasetaakreeks.

## <a name="deploy-app-configuration-data-with-your-application"></a>App-configuratiegegevens implementeren met uw toepassing

Uw toepassing kan niet worden uitgevoerd als deze afhankelijk is van de configuratie van Azure-apps en deze niet kan bereiken. Verbeter de tolerantie van uw toepassing door configuratiegegevens te verpakken in een bestand dat is geïmplementeerd met de toepassing en lokaal wordt geladen tijdens het opstarten van de toepassing. Deze aanpak garandeert dat uw toepassing standaardinstellingswaarden heeft bij het opstarten. Deze waarden worden overschreven door nieuwere wijzigingen in een app-configuratiearchief wanneer deze beschikbaar zijn.

Met [de](./howto-import-export-data.md#export-data) exportfunctie van Azure App Configuration u het proces automatiseren van het ophalen van huidige configuratiegegevens als één bestand. U dit bestand vervolgens insluiten in een build- of implementatiestap in uw CI/CD-pijplijn (continuous integration en continuous deployment).

In het volgende voorbeeld ziet u hoe u app-configuratiegegevens opneemt als een buildstap voor de web-app die in de quickstarts wordt geïntroduceerd. Voordat u verdergaat, [voltooit u eerst een ASP.NET Core-app met app-configuratie.](./quickstart-aspnet-core-app.md)

U elke codeeditor gebruiken om de stappen in deze zelfstudie uit te voeren. [Visual Studio Code](https://code.visualstudio.com/) is een uitstekende optie die beschikbaar is op de Windows-, macOS- en Linux-platforms.

### <a name="prerequisites"></a>Vereisten

Als u lokaal bouwt, downloadt en installeert u de [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) als u dat nog niet hebt gedaan.

Als u een cloudbuild wilt maken, moet u bijvoorbeeld met Azure DevOps ervoor zorgen dat de [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) in uw buildsysteem is geïnstalleerd.

### <a name="export-an-app-configuration-store"></a>Een app-configuratiearchief exporteren

1. Open uw *.csproj-bestand* en voeg het volgende script toe:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```
1. Open *Program.cs*en `CreateWebHostBuilder` werk de methode bij om het `config.AddJsonFile()` geëxporteerde JSON-bestand te gebruiken door de methode aan te roepen.  Voeg `System.Reflection` ook de naamruimte toe.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

### <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Stel een omgevingsvariabele met de naam **ConnectionString**in en stel deze in op de toegangssleutel voor uw App Configuration Store. 
    Als u de opdrachtprompt van Windows gebruikt, voert u de volgende opdracht uit en start u de opdrachtprompt opnieuw om de wijziging van kracht te laten worden:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Als u de app wilt bouwen met de .NET Core CLI, voert u de volgende opdracht uit in de opdrachtshell:

        dotnet build

3. Nadat de build is voltooid, voert u de volgende opdracht uit om de web-app lokaal uit te voeren:

        dotnet run

4. Open een browservenster `http://localhost:5000`en ga naar , dat is de standaard URL voor de web-app lokaal gehost.

    ![Quickstart voor het lokaal starten van een app](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure App-configuratiegegevens geëxporteerd om te worden gebruikt in een implementatiepijplijn. Ga voor meer informatie over het gebruik van app-configuratie verder naar de Azure CLI-voorbeelden.

> [!div class="nextstepaction"]
> [Beheerde identiteitsintegratie](./howto-integrate-azure-managed-service-identity.md)
