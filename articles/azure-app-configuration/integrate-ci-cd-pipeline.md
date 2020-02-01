---
title: 'Zelf studie: integreren met een pijp lijn voor continue integratie en levering'
titleSuffix: Azure App Configuration
description: In deze zelf studie leert u hoe u een configuratie bestand genereert met behulp van gegevens in Azure-app configuratie tijdens continue integratie en levering
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: cd40b52c20a3cafdbbeef093b574d44b9163c7b2
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899387"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integreren met een CI/CD-pijplijn

In dit artikel worden verschillende manieren beschreven om gegevens te gebruiken van Azure-app configuratie in een continue integratie en doorlopend implementatie systeem.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>App-configuratie in uw Azure DevOps-pijp lijn gebruiken

Als u een Azure DevOps-pijp lijn hebt, kunt u sleutel waarden ophalen uit de app-configuratie en deze instellen als taak variabelen. De [DevOps-extensie voor de Azure-app configuratie](https://go.microsoft.com/fwlink/?linkid=2091063) is een invoeg toepassings module die deze functionaliteit biedt. Volg de instructies voor het gebruik van de extensie in een taken reeks voor Build of release.

## <a name="deploy-app-configuration-data-with-your-application"></a>App-configuratie gegevens implementeren met uw toepassing

Uw toepassing kan mogelijk niet worden uitgevoerd als deze afhankelijk is van Azure-app configuratie en deze niet kan bereiken. U kunt de tolerantie van uw toepassing verbeteren om een dergelijke gebeurtenis te behandelen, maar dit is echter niet waarschijnlijk. Als u dit wilt doen, moet u de huidige configuratie gegevens inpakken in een bestand dat met de toepassing is geïmplementeerd en lokaal worden geladen tijdens het opstarten. Deze aanpak zorgt ervoor dat de standaard instellings waarden van uw toepassing mini maal zijn. Deze waarden worden overschreven door nieuwere wijzigingen in een app-configuratie archief wanneer deze beschikbaar zijn.

Met de functie [exporteren](./howto-import-export-data.md#export-data) van Azure-app configuratie kunt u het proces voor het ophalen van de huidige configuratie gegevens als één bestand automatiseren. Sluit dit bestand vervolgens in in een build-of implementatie stap in uw pijp lijn voor continue integratie en continue implementatie (CI/CD).

In het volgende voor beeld ziet u hoe u app-configuratie gegevens opneemt als een build-stap voor de web-app die is geïntroduceerd in de Quick starts. Voordat u doorgaat, moet u eerst [een ASP.net core-app maken met de app-configuratie](./quickstart-aspnet-core-app.md) .

U kunt elke code-editor gebruiken om de stappen in deze zelf studie uit te voeren. [Visual Studio code](https://code.visualstudio.com/) is een uitstekende optie die beschikbaar is op de Windows-, macOS-en Linux-platformen.

### <a name="prerequisites"></a>Vereisten

Als u lokaal bouwt, downloadt en installeert u de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) als u dat nog niet hebt gedaan.

Als u een Cloud-build wilt maken, kunt u met Azure DevOps bijvoorbeeld controleren of de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) is geïnstalleerd in uw build-systeem.

### <a name="export-an-app-configuration-store"></a>Een app-configuratie archief exporteren

1. Open uw *csproj* -bestand en voeg het volgende script toe:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Voeg de *Connections Tring* gekoppeld aan uw app-configuratie archief toe als een omgevings variabele.

2. Open *Program.cs*en werk de `CreateWebHostBuilder`-methode bij om het GEËXPORTEERDe JSON-bestand te gebruiken door de `config.AddJsonFile()`-methode aan te roepen.

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

1. Stel een omgevings variabele met de naam **Connections Tring**in en stel deze in op de toegangs sleutel voor uw app-configuratie archief. Als u de Windows-opdracht prompt gebruikt, voert u de volgende opdracht uit en start u de opdracht prompt zodat de wijziging kan worden doorgevoerd:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Als u Windows Power shell gebruikt, voert u de volgende opdracht uit:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Als u de app wilt bouwen met behulp van de .NET Core SLI, voert u de volgende opdracht uit in de opdracht shell:

        dotnet build

3. Wanneer de build is voltooid, voert u de volgende opdracht uit om de web-app lokaal uit te voeren:

        dotnet run

4. Open een browser venster en ga naar `http://localhost:5000`. Dit is de standaard-URL voor de web-app die lokaal wordt gehost.

    ![Quickstart voor het lokaal starten van een app](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u Azure-app configuratie gegevens geëxporteerd die moeten worden gebruikt in een implementatie pijplijn. Ga verder met de voor beelden van Azure CLI voor meer informatie over het gebruik van app-configuratie.

> [!div class="nextstepaction"]
> [Beheerde identiteits integratie](./howto-integrate-azure-managed-service-identity.md)
