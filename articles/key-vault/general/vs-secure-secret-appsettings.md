---
title: Veilige besparing van geheime toepassingsinstellingen voor een webtoepassing - Azure Key Vault | Microsoft Documenten
description: Hoe geheime toepassingsinstellingen zoals Azure-referenties of API-sleutels van derden veilig kunnen worden opgeslagen met behulp van ASP.NET core Key Vault Provider, User Secret of .NET 4.7.1-configuratiebouwers
services: visualstudio
author: cawaMS
manager: paulyuk
editor: ''
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: cawa
ms.openlocfilehash: bcacd5d2ed9e325383ec7ae75002ae0a6213111c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429757"
---
# <a name="securely-save-secret-application-settings-for-a-web-application"></a>Veilige toepassinginstellingen opslaan voor een webtoepassing

## <a name="overview"></a>Overzicht
In dit artikel wordt beschreven hoe u veilig geheime toepassingsconfiguratie-instellingen voor Azure-toepassingen opslaan.

Traditioneel worden alle configuratie-instellingen van webtoepassingen opgeslagen in configuratiebestanden zoals Web.config. Deze praktijk leidt tot het inchecken van geheime instellingen, zoals Cloud-referenties voor openbare broncontrolesystemen zoals GitHub. Ondertussen kan het moeilijk zijn om de beste beveiligingsprocedures te volgen vanwege de overhead die nodig is om de broncode te wijzigen en de ontwikkelingsinstellingen opnieuw te configureren.

Om ervoor te zorgen dat het ontwikkelingsproces veilig is, worden tooling- en frameworkbibliotheken gemaakt om de geheime instellingen van toepassingen veilig op te slaan met minimale of geen wijziging van de broncode.

## <a name="aspnet-and-net-core-applications"></a>ASP.NET- en .NET Core-toepassingen

### <a name="save-secret-settings-in-user-secret-store-that-is-outside-of-source-control-folder"></a>Geheime instellingen opslaan in het winkel met gebruikersgeheim die zich buiten de map bronbeheer bevindt
Als u een snel prototype doet of als u geen toegang tot internet hebt, begint u met het verplaatsen van uw geheime instellingen buiten de map bronbeheer naar de geheime winkel van gebruiker. User Secret store is een bestand dat is opgeslagen onder de map van de gebruikersprofiler, zodat geheimen niet worden ingecheckt bij het bronbeheer. In het volgende diagram ziet u hoe [User Secret](https://docs.microsoft.com/aspnet/core/security/app-secrets?tabs=visual-studio) werkt.

![User Secret houdt geheime instellingen buiten broncontrole](../media/vs-secure-secret-appsettings/aspnetcore-usersecret.PNG)

Als u de .NET-kernconsoletoepassing uitvoert, gebruikt u Key Vault om uw geheim veilig op te slaan.

### <a name="save-secret-settings-in-azure-key-vault"></a>Geheime instellingen opslaan in Azure Key Vault
Als u een project ontwikkelt en de broncode veilig moet delen, gebruikt u [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

1. Maak een Key Vault in uw Azure-abonnement. Vul alle vereiste velden op de gebruikersinterface in en klik op *Maken* onder aan het blad

    ![Azure Key Vault maken](../media/vs-secure-secret-appsettings/create-keyvault.PNG)

2. Geef u en uw teamleden toegang tot de Key Vault. Als u een groot team hebt, u een [Azure Active Directory-groep](../../active-directory/active-directory-groups-create-azure-portal.md) maken en die beveiligingsgroeptoegang toevoegen aan de Key Vault. Controleer in de vervolgkeuzelijst *Geheime machtigingen* de controle *Op vragen* en *lijst* onder Secret *Management Operations*.
Als u uw web-app al hebt gemaakt, verleent u de web-app toegang tot de Key Vault, zodat deze toegang heeft tot de sleutelkluis zonder geheime configuratie op te slaan in app-instellingen of bestanden. Zoek naar uw web-app op zijn naam en voeg deze toe op dezelfde manier als gebruikers toegang verlenen.

    ![Toegangsbeleid voor sleutelkluis toevoegen](../media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

3. Voeg uw geheim toe aan Key Vault op de Azure-portal. Voor geneste configuratie-instellingen vervangt u ':' door '--' zodat de geheime naam Key Vault geldig is. ':' mag niet op naam staan van een Key Vault-geheim.

    ![Sleutelkluisgeheim toevoegen](../media/vs-secure-secret-appsettings/add-keyvault-secret.png)

    > [!NOTE]
    > Voorafgaand aan Visual Studio 2017 V15.6 hebben we aanbevolen de Azure Services Authentication-extensie voor Visual Studio te installeren. Maar het is nu afgeschaft als de functionaliteit is geïntegreerd in de Visual Studio . Als u een oudere versie van visual Studio 2017 gebruikt, raden we u dan ook aan om te updaten naar ten minste VS 2017 15.6 of ouder, zodat u deze functionaliteit native gebruiken en toegang hebt tot de Key-vault via de Visual Studio-aanmeldingsidentiteit zelf.
    >

4. Voeg de volgende NuGet-pakketten toe aan uw project:

    ```
    Microsoft.Azure.KeyVault
    Microsoft.Azure.Services.AppAuthentication
    Microsoft.Extensions.Configuration.AzureKeyVault
    ```
5. Voeg de volgende code toe aan Program.cs bestand:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
             Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((ctx, builder) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        builder.AddAzureKeyVault(
                        keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });

        private static string GetKeyVaultEndpoint() => Environment.GetEnvironmentVariable("KEYVAULT_ENDPOINT");
    ```
6. Voeg de URL van Key Vault toe aan het bestand settings.json. De naam van de omgevingsvariabele *KEYVAULT_ENDPOINT* wordt gedefinieerd in de code die u in stap 6 hebt toegevoegd.

    ![Url van Key Vault toevoegen als een projectomgevingsvariabele](../media/vs-secure-secret-appsettings/add-keyvault-url.png)

7. Begin met het opsporen van het project. Het moet succesvol worden uitgevoerd.

## <a name="aspnet-and-net-applications"></a>ASP.NET- en .NET-toepassingen

.NET 4.7.1 ondersteunt Key Vault- en Secret-configuratiebouwers, waardoor geheimen buiten de broncontrolemap kunnen worden verplaatst zonder codewijzigingen.
Download [.NET 4.7.1](https://www.microsoft.com/download/details.aspx?id=56115) om verder te gaan en migreert uw toepassing als deze een oudere versie van .NET-framework gebruikt.

### <a name="save-secret-settings-in-a-secret-file-that-is-outside-of-source-control-folder"></a>Geheime instellingen opslaan in een geheim bestand dat zich buiten de map bronbeheer bevindt
Als u een snel prototype schrijft en geen Azure-resources wilt inrichten, gaat u met deze optie.

1. Installeer het volgende NuGet-pakket voor uw project
    ```
    Microsoft.Configuration.ConfigurationBuilders.Base
    ```

2. Maak een bestand dat vergelijkbaar is met het volgende. Sla deze op onder een locatie buiten uw projectmap.

    ```xml
    <root>
        <secrets ver="1.0">
            <secret name="secret1" value="foo_one" />
            <secret name="secret2" value="foo_two" />
        </secrets>
    </root>
    ```

3. Definieer het geheime bestand als een configuratiebouwer in uw Web.config-bestand. Plaats deze sectie voor de sectie *AppInstellingen.*

    ```xml
    <configBuilders>
        <builders>
            <add name="Secrets"
                 secretsFile="C:\Users\AppData\MyWebApplication1\secret.xml" type="Microsoft.Configuration.ConfigurationBuilders.UserSecretsConfigBuilder,
                    Microsoft.Configuration.ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```

4. AppInstellingen opgeven maakt gebruik van de geheime configuratiebouwer. Zorg ervoor dat er een vermelding voor de geheime instelling met een dummy waarde.

    ```xml
        <appSettings configBuilders="Secrets">
            <add key="webpages:Version" value="3.0.0.0" />
            <add key="webpages:Enabled" value="false" />
            <add key="ClientValidationEnabled" value="true" />
            <add key="UnobtrusiveJavaScriptEnabled" value="true" />
            <add key="secret" value="" />
        </appSettings>
    ```

5. Debuging van uw app. Het moet succesvol worden uitgevoerd.

### <a name="save-secret-settings-in-an-azure-key-vault"></a>Geheime instellingen opslaan in een Azure Key Vault
Volg instructies uit ASP.NET kernsectie om een Key Vault voor uw project te configureren.

1. Installeer het volgende NuGet-pakket voor uw project
   ```
   Microsoft.Configuration.ConfigurationBuilders.UserSecrets
   ```

2. Definieer Key Vault-configuratiebouwer in Web.config. Plaats deze sectie voor de sectie *AppInstellingen.* Vervang *vaultName* als de naam Key Vault als uw Sleutelkluis in openbaar Azure staat of volledige URI als u Sovereign-cloud gebruikt.

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>
    <configBuilders>
        <builders>
            <add name="AzureKeyVault" vaultName="Test911" type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, ConfigurationBuilders, Version=1.0.0.0, Culture=neutral" />
        </builders>
    </configBuilders>
    ```
3. AppInstellingen opgeven, gebruikt de configuratiebouwer Key Vault. Zorg ervoor dat er een vermelding voor de geheime instelling met een dummy waarde.

   ```xml
   <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="secret" value="" />
   </appSettings>
   ```

4. Begin met het opsporen van het project. Het moet succesvol worden uitgevoerd.
