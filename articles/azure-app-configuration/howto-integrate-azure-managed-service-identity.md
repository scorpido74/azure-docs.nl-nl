---
title: Integreren met door Azure beheerde identiteiten | Microsoft Docs
description: Meer informatie over het gebruik van door Azure beheerde identiteiten om te verifiëren en toegang te krijgen tot Azure-app configuratie
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 3a5517c31cdac0bf6f5ea386a8614d15521d4479
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035534"
---
# <a name="integrate-with-azure-managed-identities"></a>Integreren met door Azure beheerde identiteiten

Met Azure Active Directory [beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) kunt u het beheer van geheimen vereenvoudigen voor uw Cloud toepassing. Met een beheerde identiteit kunt u uw code instellen voor het gebruik van de service-principal die is gemaakt voor de service Azure Compute waarop deze wordt uitgevoerd. U gebruikt een beheerde identiteit in plaats van een afzonderlijke referentie die is opgeslagen in Azure Key Vault of een lokale connection string. 

Azure-app configuratie en de .NET core-, .NET-en Java lente-client bibliotheken worden geleverd met beheerde service-identiteit (MSI) ingebouwde ondersteuning. Hoewel u dit niet nodig hebt, kunt u met MSI geen toegangs tokens meer gebruiken die geheimen bevatten. De code heeft toegang tot de app-configuratie opslag door alleen het service-eind punt te gebruiken. U kunt deze URL rechtstreeks insluiten in uw code, zonder dat u een geheim weergeeft.

In deze zelfstudie leert hoe u kunt profiteren van MSI voor toegang tot App-configuratie. Dit is gebaseerd op de web-app die is geïntroduceerd in de quickstarts. Voordat u doorgaat, moet u eerst [een ASP.net core-app maken met de app-configuratie](./quickstart-aspnet-core-app.md) .

Daarnaast ziet u in deze zelf studie hoe u MSI kunt gebruiken in combi natie met de Key Vault verwijzingen van de app-configuratie. Zo kunt u probleemloos toegang krijgen tot geheimen die zijn opgeslagen in Key Vault en configuratie waarden in app-configuratie. Als u deze functie wilt verkennen, kunt u het [gebruik van Key Vault verwijzingen met ASP.net core](./use-key-vault-references-dotnet-core.md) eerst volt ooien.

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

Als u een beheerde identiteit in de portal wilt instellen, maakt u eerst een toepassing als normaal en schakelt u de functie in.

1. Maak een App Services-exemplaar in het [Azure Portal](https://portal.azure.com) zoals u dat gewend bent. Ga naar de portal.

2. Schuif omlaag naar de **instellingen** groep in het linkerdeel venster en selecteer **identiteit**.

3. Schakel op het tabblad **toegewezen systeem** de optie **status** in **op** aan en selecteer **Opslaan**.

4. Antwoord **Ja** wanneer u wordt gevraagd door het systeem toegewezen beheerde identiteit in te scha kelen.

    ![Stel beheerde identiteit in App Service in](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>Verleen toegang tot de App-configuratie

1. Selecteer in het [Azure Portal](https://portal.azure.com) **alle resources** en selecteer de app-configuratie opslag die u hebt gemaakt in de Quick Start.

1. Selecteer **toegangsbeheer (IAM)** .

1. Klik op het tabblad **toegang controleren** op **toevoegen** in de gebruikers interface van de **roltoewijzing toevoegen** .

1. Selecteer onder **rol** **Inzender**. Onder **toegang toewijzen aan**selecteert u **app service** onder door het **systeem toegewezen beheerde identiteit**.

1. Selecteer uw Azure-abonnement onder **abonnement**. Selecteer de App Service Resource voor uw app.

1. Selecteer **Opslaan**.

    ![Een beheerde identiteit toevoegen](./media/add-managed-identity.png)

1. Optioneel: Als u ook toegang tot Key Vault wilt verlenen, volgt u de instructies in [Key Vault verificatie bieden met een beheerde identiteit](https://docs.microsoft.com/azure/key-vault/managed-identity).

## <a name="use-a-managed-identity"></a>Een beheerde identiteit gebruiken

1. Zoek de URL naar de app-configuratie in het configuratie scherm van de Azure Portal en klik vervolgens op het tabblad **toegangs toetsen** .

1. Open *appSettings. json*en voeg het volgende script toe. Vervang *\<service_endpoint >* , met inbegrip van de haakjes, door de URL naar uw app-configuratie archief. 

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

1. Als u alleen toegang wilt krijgen tot waarden die rechtstreeks in de app-configuratie zijn opgeslagen, opent u *Program.cs*en werkt u de `CreateWebHostBuilder`-methode bij door de `config.AddAzureAppConfiguration()`-methode te vervangen.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

1. Als u app-configuratie waarden wilt gebruiken, evenals Key Vault verwijzingen, opent u *Program.cs*en werkt u de `CreateWebHostBuilder`-methode bij, zoals hieronder wordt weer gegeven. Hiermee wordt een nieuwe `KeyVaultClient` gemaakt met behulp van een `AzureServiceTokenProvider` en wordt deze verwijzing door gegeven aan een aanroep van de methode `UseAzureKeyVault`.

    ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    var settings = config.Build();
                    AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                    KeyVaultClient kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                    
                    config.AddAzureAppConfiguration(options => options.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"])).UseAzureKeyVault(kvClient));
                })
                .UseStartup<Startup>();
    ```

    U hebt nu toegang tot Key Vault verwijzingen, net zoals elke andere app-configuratie sleutel. De configuratie provider gebruikt de `KeyVaultClient` die u hebt geconfigureerd om te verifiëren bij Key Vault en de waarde op te halen.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>Implementeren vanuit lokale Git

De eenvoudigste manier om lokale Git-implementatie voor uw app in te scha kelen met de kudu-buildserver is door gebruik te maken van Azure Cloud Shell.

### <a name="configure-a-deployment-user"></a>Een implementatiegebruiker configureren

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Lokale Git met Kudu inschakelen
Als u geen lokale Git-opslag plaats voor uw app hebt, moet u er een initialiseren. U doet dit door de volgende opdrachten uit te voeren vanuit de projectmap van uw app:

```cmd
git init
git add .
git commit -m "Initial version"
```

Als u lokale Git-implementatie voor uw app met de kudu-build-server wilt inschakelen, voert u [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) in Cloud shell uit.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Als u in plaats daarvan een app met Git wilt maken, voert u [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) in Cloud shell uit met de para meter `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

De `az webapp create` opdracht geeft een resultaat die vergelijkbaar is met de volgende uitvoer:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Uw project implementeren

Voeg, eenmaal terug in het _lokale terminalvenster_, een externe Azure-instantie toe aan uw lokale Git-opslagplaats. Vervang _\<url >_ met de URL van de externe Git-instantie die u hebt verkregen via [Git inschakelen voor uw app](#enable-local-git-with-kudu).

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

App-configuratie-providers voor .NET Framework en Java Spring hebben ook ingebouwde ondersteuning voor beheerde identiteit. In deze gevallen gebruikt u het URL-eind punt van uw app-configuratie archief in plaats van de volledige connection string wanneer u een provider configureert. Voor de .NET Framework-console-app die u in de Quick Start hebt gemaakt, geeft u bijvoorbeeld de volgende instellingen op in het bestand *app. config* :

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

> [!div class="nextstepaction"]
> [CLI-voorbeelden](./cli-samples.md)
