---
title: Quickstart voor Azure-appconfiguratie met .NET Framework | Microsoft Docs
description: Een quickstart voor het gebruik van Azure-app-configuratie met .NET Framework-apps
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: 1c56088a2c51c50c7f9cf1ff1e790d580fdb08d8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245392"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Snelstart: een .NET Framework-app maken met Azure-app-configuratie

In deze quickstart neemt u Azure App Configuration op in een .NET Framework-gebaseerde console-app om opslag en beheer van toepassingsinstellingen te centraliseren, gescheiden van uw code.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Een app-configuratiearchief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecteer **Configuratieverkenner** > **Sleutelwaarde maken** > **Key-value** om de volgende sleutelwaardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | TestApp:Settings:Message | Gegevens van Azure App Configuration |

    Laat **label en** **inhoudstype** voorlopig leeg.

7. Selecteer **Toepassen**.

## <a name="create-a-net-console-app"></a>Een .NET Core-consoletoepassing maken

1. Start Visual Studio en selecteer Nieuw**New** > **project** **bestand** > .

1. Filter **in Een nieuw project maken**op het **projecttype Console** en klik op **Console-app (.NET Framework).** Selecteer **Volgende**.

1. Voer in **Uw nieuwe project configureren**een projectnaam in. Selecteer **onder Framework** **.NET Framework 4.7.1** of hoger. Selecteer **Maken**.

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een app-configuratiearchief

1. Klik met de rechtermuisknop op uw project en selecteer **NuGet-pakketten beheren.** Zoek **op het** tabblad Bladeren en voeg de volgende NuGet-pakketten toe aan uw project. Als u ze niet vinden, schakelt u het selectievakje **Prerelease opnemen** in.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Werk het *bestand App.config* van uw project als volgt bij:

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

   De verbindingstekenreeks van uw app-configuratiearchief `ConnectionString`wordt gelezen vanuit de omgevingsvariabele. Voeg `Environment` de configuratiebouwer `MyConfigStore` toe `configBuilders` vóór de eigenschap in de `appSettings` sectie.

1. Open *Program.cs*en `Main` werk de methode bij `ConfigurationManager`om App-configuratie te gebruiken door aan te roepen .

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Stel een omgevingsvariabele met de naam **ConnectionString** in op de verbindingstekenreeks van uw App Configuration Store. Als u de opdrachtprompt van Windows gebruikt, voert u de volgende opdracht uit:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```
1. Start Visual Studio opnieuw op om de wijziging van kracht te laten worden. Druk op Ctrl + F5 om de console-app te bouwen en uit te voeren.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een nieuwe app-configuratiewinkel gemaakt en deze gebruikt met een .NET Framework-console-app. De `AppSettings` waarde `ConfigurationManager` van de waarde wordt niet gewijzigd nadat de toepassing is gestart. De app Configuration .NET Standard-configuratieproviderbibliotheek kan echter ook worden gebruikt in een .NET Framework-app. Ga door naar de volgende zelfstudie voor meer informatie over het inschakelen van de .NET Framework-app om configuratie-instellingen dynamisch te vernieuwen.

> [!div class="nextstepaction"]
> [Dynamische configuratie inschakelen](./enable-dynamic-configuration-dotnet.md)
