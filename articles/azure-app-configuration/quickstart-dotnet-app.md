---
title: Quickstart voor Azure-appconfiguratie met .NET Framework | Microsoft Docs
description: Een quickstart voor het gebruik van Azure-app-configuratie met .NET Framework-apps
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/09/2019
ms.author: lcozzens
ms.openlocfilehash: f1fd1c21789c272bf8deae72ef64f2bbb1c6b3cb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707436"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Snelstartgids: een .NET Framework-app maken met Azure-app configuratie

In deze Snelstartgids neemt u Azure-app configuratie op in een op .NET Framework gebaseerde console-app om de opslag en het beheer van toepassings instellingen gescheiden van uw code te centraliseren.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Een app-configuratie archief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecteer **configuratie verkenner** >  **+ maken** om de volgende sleutel-waardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration |

    Laat het **Label** en het **inhouds type** nu leeg.

## <a name="create-a-net-console-app"></a>Een .NET Core-consoletoepassing maken

1. Start Visual Studio en selecteer **bestand** > **Nieuw** > **project**.

1. In **een nieuw project maken**filtert u op het type **console** project en klikt u op **console-app (.NET Framework)** . Klik op **Volgende**.

1. Voer in **uw nieuwe project configureren**een project naam in. Onder **Framework**selecteert u **.NET Framework 4.7.1** of hoger. Klik op **Maken**.

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een app-configuratie archief

1. Klik met de rechter muisknop op het project en selecteer **NuGet-pakketten beheren**. Zoek op het tabblad **Bladeren** de volgende NuGet-pakketten aan uw project en voeg deze toe. Als u deze niet kunt vinden, schakelt u het selectie vakje **include Prerelease** in.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Werk het bestand *app. config* van uw project als volgt bij:

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   De connection string van uw app-configuratie archief wordt gelezen uit de omgevings variabele `ConnectionString`. Voeg de `Environment` Configuration Builder toe vóór de `MyConfigStore` in de eigenschap `configBuilders` van de sectie `appSettings`.

1. Open *Program.cs*en werk de `Main`-methode bij om app-configuratie te gebruiken door `ConfigurationManager`aan te roepen.

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>De app lokaal bouwen en uitvoeren

1. Stel een omgevings variabele met de naam **Connections Tring** in op de Connection String van de app-configuratie opslag. Als u de Windows-opdracht prompt gebruikt, voert u de volgende opdracht uit:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Als u Windows Power shell gebruikt, voert u de volgende opdracht uit:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Start Visual Studio opnieuw zodat de wijziging kan worden doorgevoerd. Druk op CTRL + F5 om de console-app te bouwen en uit te voeren.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u een nieuwe app-configuratie opgeslagen gemaakt en gebruikt met een .NET Framework-console-app. De waarde `AppSettings` van `ConfigurationManager` wordt niet gewijzigd nadat de toepassing is gestart. De bibliotheek van de app-configuratie provider .NET Standard kan echter ook worden gebruikt in een .NET Framework-app. Ga door naar de volgende zelf studie voor meer informatie over het inschakelen van uw .NET Framework-app om configuratie-instellingen dynamisch te vernieuwen.

> [!div class="nextstepaction"]
> [Dynamische configuratie inschakelen](./enable-dynamic-configuration-dotnet.md)
