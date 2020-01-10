---
title: Integreren met door Azure beheerde identiteiten
description: Meer informatie over het gebruik van door Azure beheerde identiteiten om te verifiëren en toegang te krijgen tot Azure-app configuratie
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 12/29/2019
ms.author: lcozzens
ms.openlocfilehash: 7461f378a4f95a43971f5893fe70739511e942ff
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75731998"
---
# <a name="integrate-with-azure-managed-identities"></a>Integreren met Azure Managed Identities

Met Azure Active Directory [beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) kunt u het beheer van geheimen vereenvoudigen voor uw Cloud toepassing. Met een beheerde identiteit kan uw code gebruikmaken van de service-principal die is gemaakt voor de Azure-service waarop deze wordt uitgevoerd. U gebruikt een beheerde identiteit in plaats van een afzonderlijke referentie die is opgeslagen in Azure Key Vault of een lokale connection string. 

Azure-app-configuratie en de .NET core-, .NET Framework-en Java lente-client bibliotheken hebben beheerde identiteits ondersteuning ingebouwd. Hoewel u deze niet hoeft te gebruiken, elimineert de beheerde identiteit geen toegangs token dat geheimen bevat. De code heeft toegang tot de app-configuratie opslag door alleen het service-eind punt te gebruiken. U kunt deze URL rechtstreeks insluiten in uw code, zonder dat u een geheim weergeeft.

In deze zelf studie ziet u hoe u kunt profiteren van de beheerde identiteit om toegang te krijgen tot de app-configuratie. Dit is gebaseerd op de web-app die is geïntroduceerd in de quickstarts. Voordat u doorgaat, moet u eerst [een ASP.net core-app maken met de app-configuratie](./quickstart-aspnet-core-app.md) .

In deze zelf studie ziet u ook hoe u de beheerde identiteit kunt gebruiken in combi natie met de Key Vault verwijzingen van de app-configuratie. Met één beheerde identiteit kunt u probleemloos toegang krijgen tot beide geheimen van Key Vault en configuratie waarden van de app-configuratie. Als u deze functie wilt verkennen, kunt u het [gebruik van Key Vault verwijzingen met ASP.net core](./use-key-vault-references-dotnet-core.md) eerst volt ooien.

U kunt elke code-editor gebruiken om de stappen in deze zelf studie uit te voeren. [Visual Studio code](https://code.visualstudio.com/) is een uitstekende optie die beschikbaar is op de Windows-, macOS-en Linux-platformen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Verleen een beheerde identiteit toegang tot de app-configuratie.
> * Configureer uw app voor het gebruik van een beheerde identiteit wanneer u verbinding maakt met de app-configuratie.
> * U kunt uw app eventueel configureren voor het gebruik van een beheerde identiteit wanneer u verbinding maakt met Key Vault via een app-configuratie Key Vault verwijzing.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze zelfstudie te voltooien:

* [.Net core SDK](https://www.microsoft.com/net/download/windows).
* [Azure Cloud shell geconfigureerd](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>Een beheerde identiteit toevoegen

Als u een beheerde identiteit in de portal wilt instellen, maakt u eerst een toepassing en schakelt u de functie in.

1. Maak een App Services-exemplaar in het [Azure Portal](https://portal.azure.com) zoals u dat gewend bent. Ga naar de portal.

1. Schuif omlaag naar de **instellingen** groep in het linkerdeel venster en selecteer **identiteit**.

1. Schakel op het tabblad **toegewezen systeem** de optie **status** in **op** aan en selecteer **Opslaan**.

1. Antwoord **Ja** wanneer u wordt gevraagd door het systeem toegewezen beheerde identiteit in te scha kelen.

    ![Stel beheerde identiteit in App Service in](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Verleen toegang tot de App-configuratie

1. Selecteer in het [Azure Portal](https://portal.azure.com) **alle resources** en selecteer de app-configuratie opslag die u hebt gemaakt in de Quick Start.

1. Klik op **Toegangsbeheer (IAM)** .

1. Klik op het tabblad **toegang controleren** op **toevoegen** in de gebruikers interface van de **roltoewijzing toevoegen** .

1. Onder **rol**selecteert u **app Configuration Data Reader**. Onder **toegang toewijzen aan**selecteert u **app service** onder door het **systeem toegewezen beheerde identiteit**.

1. Selecteer uw Azure-abonnement onder **abonnement**. Selecteer de App Service Resource voor uw app.

1. Selecteer **Opslaan**.

    ![Een beheerde identiteit toevoegen](./media/add-managed-identity.png)

1. Optioneel: als u ook toegang tot Key Vault wilt verlenen, volgt u de instructies in [Key Vault verificatie bieden met een beheerde identiteit](https://docs.microsoft.com/azure/key-vault/managed-identity).

## <a name="use-a-managed-identity"></a>Een beheerde identiteit gebruiken

1. Voeg een verwijzing naar het pakket *Azure. Identity* toe:

    ```cli
    dotnet add package Azure.Identity --version 1.1.0
    ```

1. Zoek het eind punt naar het configuratie archief van uw app. Deze URL wordt weer gegeven op het tabblad **toegangs sleutels** voor de Store in de Azure Portal.

1. Open *appSettings. json*en voeg het volgende script toe. Vervang *\<service_endpoint >* , met inbegrip van de vier Kante haken, door de URL naar uw app-configuratie archief. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Open *Program.cs*en voeg een verwijzing naar de `Azure.Identity` en `Microsoft.Azure.Services.AppAuthentication` naam ruimten toe:

    ```csharp-interactive
    using Azure.Identity;
    using Microsoft.Azure.Services.AppAuthentication;
    ```

1. Als u alleen toegang wilt krijgen tot waarden die rechtstreeks in de app-configuratie zijn opgeslagen, werkt u de `CreateWebHostBuilder` methode bij door de `config.AddAzureAppConfiguration()`-methode te vervangen.

    > [!IMPORTANT]
    > `CreateHostBuilder` vervangt `CreateWebHostBuilder` in .NET Core 3,0.  Selecteer de juiste syntaxis op basis van uw omgeving.

    ### <a name="net-core-2xtabcore2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>();
    ```

    ### <a name="net-core-3xtabcore3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()));
                })
                .UseStartup<Startup>());
    ```
    ---

1. Als u zowel app-configuratie waarden als Key Vault referenties wilt gebruiken, moet u *Program.cs* bijwerken zoals hieronder wordt weer gegeven. Met deze code wordt een nieuwe `KeyVaultClient` gemaakt met behulp van een `AzureServiceTokenProvider` en wordt deze verwijzing door gegeven aan een aanroep van de `UseAzureKeyVault`-methode.

    ### <a name="net-core-2xtabcore2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
            public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
                WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration((hostingContext, config) =>
                    {
                        var settings = config.Build();
                        AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                        KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                        
                        config.AddAzureAppConfiguration(options => options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()).UseAzureKeyVault(kvClient));
                    })
                    .UseStartup<Startup>();
    ```

    ### <a name="net-core-3xtabcore3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                        AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                        KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                        
                        config.AddAzureAppConfiguration(options => options.Connect(new Uri(settings["AppConfig:Endpoint"]), new ManagedIdentityCredential()).UseAzureKeyVault(kvClient));
                    })
                    .UseStartup<Startup>());
    ```
    ---

    U hebt nu toegang tot Key Vault verwijzingen, net zoals elke andere app-configuratie sleutel. De configuratie provider gebruikt de `KeyVaultClient` die u hebt geconfigureerd om te verifiëren bij Key Vault en de waarde op te halen.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-from-local-git"></a>Implementeren vanuit lokale Git

De eenvoudigste manier om lokale Git-implementatie voor uw app in te scha kelen met de kudu-buildserver is door gebruik te maken van [Azure Cloud shell](https://shell.azure.com).

### <a name="configure-a-deployment-user"></a>Een implementatiegebruiker configureren

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Lokale Git met Kudu inschakelen
Als u geen lokale Git-opslag plaats voor uw app hebt, moet u er een initialiseren. Voor het initialiseren van een lokale Git-opslag plaats voert u de volgende opdrachten uit vanuit de projectmap van uw app:

```cmd
git init
git add .
git commit -m "Initial version"
```

Als u lokale Git-implementatie voor uw app wilt inschakelen met de kudu-buildserver, voert u [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) uit in Cloud shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Met deze opdracht krijgt u iets te zien dat vergelijkbaar is met de volgende uitvoer:

```json
{
  "url": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
}
```

### <a name="deploy-your-project"></a>Uw project implementeren

Voeg in het _lokale terminal venster_een externe Azure-Data Bank toe aan uw lokale Git-opslag plaats. Vervang _\<url >_ door de URL van de externe Git die u hebt ontvangen van [lokale Git inschakelen met kudu](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Push naar de externe Azure-instantie om uw app te implementeren met de volgende opdracht. Wanneer u wordt gevraagd om een wacht woord, voert u het wacht woord in dat u hebt gemaakt in [een implementatie gebruiker configureren](#configure-a-deployment-user). Gebruik niet het wacht woord dat u gebruikt om u aan te melden bij de Azure Portal.

```bash
git push azure master
```

U kunt runtime-specifieke Automation zien in de uitvoer, zoals MSBuild voor ASP.NET, `npm install` voor node. js en `pip install` voor python.

### <a name="browse-to-the-azure-web-app"></a>Bladeren naar de Azure-web-app

Blader naar uw web-app met behulp van een browser om te controleren of de inhoud is geïmplementeerd.

```bash
http://<app_name>.azurewebsites.net
```

![App die wordt uitgevoerd in App Service](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>Beheerde identiteit gebruiken in andere talen

App-configuratie-providers voor .NET Framework en Java Spring hebben ook ingebouwde ondersteuning voor beheerde identiteit. Wanneer u een van deze providers configureert, kunt u het URL-eind punt van uw Store gebruiken in plaats van de volledige connection string. 

U kunt bijvoorbeeld de .NET Framework console-app die in de Quick start is gemaakt, bijwerken om de volgende instellingen op te geven in het bestand *app. config* :

```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
```

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u een door Azure beheerde identiteit toegevoegd om de toegang tot de app-configuratie te stroom lijnen en het referentie beheer voor uw app te verbeteren. Ga verder met de voor beelden van Azure CLI voor meer informatie over het gebruik van app-configuratie.

> [!div class="nextstepaction"]
> [CLI-voorbeelden](./cli-samples.md)