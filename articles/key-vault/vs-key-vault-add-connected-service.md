---
title: Key Vault-ondersteuning toevoegen aan uw ASP.NET project met Visual Studio - Azure Key Vault | Microsoft Documenten
description: Gebruik deze zelfstudie om te leren hoe u Key Vault-ondersteuning toevoegen aan een ASP.NET of ASP.NET Core-webtoepassing.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 1c12cf4a7bd097c6d33d032065734b477920644b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456994"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Key Vault toevoegen aan uw webtoepassing met Visual Studio Connected Services

In deze zelfstudie leert u hoe u eenvoudig alles toevoegen wat u nodig hebt om Azure Key Vault te gebruiken om uw geheimen voor webprojecten in Visual Studio te beheren, of u nu ASP.NET Core of elk type ASP.NET project gebruikt. Door de functie Verbonden services in Visual Studio te gebruiken, u Visual Studio automatisch alle NuGet-pakketten en configuratie-instellingen laten toevoegen die u nodig hebt om verbinding te maken met Key Vault in Azure.

Zie Key Vault Connected Service - Wat is er [is gebeurd met mijn ASP.NET 4.7.1-project](#how-your-aspnet-framework-project-is-modified) of Key Vault Connected Service - Wat is er gebeurd met mijn ASP.NET 4.7.1-project of [Key Vault Connected Service - Wat is er gebeurd met mijn ASP.NET Core-project.](#how-your-aspnet-core-project-is-modified)

## <a name="prerequisites"></a>Vereisten

- **Een Azure-abonnement**. Als je nog geen abonnement hebt, meld je dan aan voor een [gratis account.](https://azure.microsoft.com/pricing/free-trial/)
- **Visual Studio 2019 versie 16.3** of hoger, of **Visual Studio 2017 versie 15.7** met de **Web Development-workload** geïnstalleerd. [Download nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Voor ASP.NET (niet Core) met Visual Studio 2017 hebt u de .NET Framework 4.7.1 of hoger Development Tools nodig, die niet standaard zijn geïnstalleerd. Als u ze wilt installeren, start u de Visual Studio Installer, kiest u **Wijzigen**en kiest u **vervolgens Afzonderlijke componenten**, vervolgens aan de rechterkant, breidt u ASP.NET en **webontwikkeling**uit en kiest **u .NET Framework 4.7.1 Development Tools**.
- Een ASP.NET 4.7.1 of hoger, of ASP.NET Core 2.0 of hoger webproject geopend.

## <a name="add-key-vault-support-to-your-project"></a>Key Vault-ondersteuning toevoegen aan uw project

Voordat u begint, moet u ervoor zorgen dat u bent aangemeld bij Visual Studio. Meld u aan met hetzelfde account dat u gebruikt voor uw Azure-abonnement. Open vervolgens een ASP.NET 4.7.1 of hoger of ASP.NET Core 2.0-webproject en voer de volgende stappen uit:

1. Klik in **Solution Explorer**met de rechtermuisknop op het project waaraan u de Key Vault-ondersteuning wilt toevoegen en kies Verbonden**service** **toevoegen** > .
   De Connected Service-pagina wordt weergegeven met services die u aan uw project kunt toevoegen.
1. Kies secure secrets with **Azure Key Vault**in het menu met beschikbare services.

   ![Kies 'Geheimen beveiligen met Azure Key Vault'](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Selecteer het abonnement dat u wilt gebruiken en kies vervolgens een nieuwe of bestaande Key Vault. Als u de nieuwe Key Vault kiest, wordt er een **koppeling Bewerken** weergegeven. Selecteer deze optie om uw nieuwe Key Vault te configureren.

   ![Selecteer uw abonnement](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. Voer in **Azure Key Vault bewerken**de naam in die u wilt gebruiken voor de Key Vault.

1. Selecteer een bestaande **resourcegroep**of kies ervoor om een nieuwe groep te maken met een automatisch gegenereerde unieke naam.  Als u een nieuwe groep met een andere naam wilt maken, u de [Azure-portal](https://portal.azure.com)gebruiken en de pagina sluiten en opnieuw starten om de lijst met brongroepen opnieuw te laden.
1. Kies de **locatie** waar u de sleutelkluis wilt maken. Als uw webtoepassing wordt gehost in Azure, kiest u de regio die de webtoepassing host voor optimale prestaties.
1. Kies een **prijscategorie**. Zie Key [Vault Pricing voor](https://azure.microsoft.com/pricing/details/key-vault/)meer informatie.
1. Kies **OK** om de configuratiekeuzes te accepteren.
1. Nadat u een bestaande Key Vault hebt geselecteerd of een nieuwe Key Vault hebt geconfigureerd, selecteert u op het tabblad **Azure Key Vault** van Visual Studio de optie **Toevoegen** om de verbonden service toe te voegen.
1. Selecteer de **geheimen beheren die zijn opgeslagen in deze key vault-koppeling** om de pagina **Geheimen** voor uw sleutelkluis te openen. Als u de pagina of het project hebt gesloten, u ernaar navigeren in de [Azure-portal](https://portal.azure.com) door **Alle services** te kiezen en, onder **Beveiliging,** **Key Vault**te kiezen, kies je sleutelkluis.
1. Kies in de sectie Sleutelkluis voor de sleutelkluis die u hebt gemaakt de optie **Geheimen**en **vervolgens Genereren/importeren.**

   ![Een geheim genereren/importeren](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Voer een geheim in, zoals *MySecret* en geef het als test een tekenreekswaarde en selecteer vervolgens de knop **Maken.**

   ![Een geheim maken](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (facultatief) Voer een ander geheim, maar deze keer zet het in een categorie door het benoemen *van geheimen - MySecret*. Deze syntaxis geeft een categorie "Geheimen" op die een geheim "MySecret" bevat.

Nu heb je toegang tot je geheimen in code. De volgende stappen zijn verschillend, afhankelijk van of u ASP.NET 4.7.1 of ASP.NET Core gebruikt.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Toegang tot uw geheimen in code (ASP.NET Core)

1. Klik in Solution Explorer met de rechtermuisknop op uw project en selecteer **NuGet-pakketten beheren.** Zoek en installeer op het tabblad **Bladeren** deze twee NuGet-pakketten: [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) en voor .NET Core 2, voeg [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) of voor .NET Core 3 toe voeg[Microsoft.Azure.KeyVault.Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core)toe.

1. Selecteer voor .NET Core `Program.cs` 2 het `BuildWebHost` tabblad en wijzig de definitie in de klasse Programma als volgt:

   ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
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
               }
            ).UseStartup<Startup>();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

   Voor .NET Core 3 gebruikt u de volgende code.

   ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((context, config) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        config.AddAzureKeyVault(keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    ```

1. Open vervolgens een van de paginabestanden, zoals *Index.cshtml.cs* en schrijf de volgende code:
   1. Vermeld in `Microsoft.Extensions.Configuration` deze richtlijn een verwijzing naar:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Voeg de configuratievariabele toe.

      ```csharp
      private static readonly IConfiguration _configuration;
      ```

   1. Voeg deze constructor toe of vervang de bestaande constructeur met de opdracht:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Werk de `OnGet`-methode bij. Werk de tijdelijke aanduidingswaarde bij die hier wordt weergegeven met de geheime naam die u in de bovenstaande opdrachten hebt gemaakt.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. Als u de waarde bij runtime `ViewData["Message"]` wilt bevestigen, voegt u code toe die moet worden weergegeven aan het *.cshtml-bestand* om het geheim in een bericht weer te geven.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

U de app lokaal uitvoeren om te controleren of het geheim is verkregen uit de Key Vault.

## <a name="access-your-secrets-aspnet"></a>Krijg toegang tot je geheimen (ASP.NET)

U de configuratie zo instellen dat het bestand web.config een dummywaarde heeft in het `appSettings` element dat wordt vervangen door de werkelijke waarde bij runtime. U dit vervolgens `ConfigurationManager.AppSettings` openen via de gegevensstructuur.

1. Bewerk uw web.config-bestand.  Zoek de appInstellingen-tag, `configBuilders="AzureKeyVault"`voeg een kenmerk toe en voeg een regel toe:

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. Bewerk `About` de methode in *HomeController.cs*om de waarde voor bevestiging weer te geven.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. Voer de app lokaal uit onder de foutopsporing, ga naar het tabblad **Informatie** en controleer of de waarde uit de sleutelkluis wordt weergegeven.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep als u deze niet meer nodig hebt. Hiermee worden de Key Vault en gerelateerde bronnen verwijderd. De resourcegroep verwijderen via de portal:

1. Voer de naam van uw resourcegroep in het vak Zoeken bovenaan de portal in. Wanneer u de in deze snelstart gebruikte resourcegroep in de zoekresultaten ziet, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Voer in **het vak NAAM VAN DE RESOURCEGROEP:** de naam van de resourcegroep in en selecteer **Verwijderen**.

## <a name="troubleshooting"></a>Problemen oplossen

Als uw Key Vault wordt uitgevoerd op een ander Microsoft-account dan het account waarop u bent aangemeld bij Visual Studio (de Key Vault wordt bijvoorbeeld uitgevoerd op uw werkaccount, maar Visual Studio uw privé-account gebruikt) krijgt u een foutmelding in uw Program.cs-bestand , dat Visual Studio geen toegang kan krijgen tot de Key Vault. Dit probleem oplossen:

1. Ga naar de [Azure-portal](https://portal.azure.com) en open uw Key Vault.

1. Kies **Toegangsbeleid**en **voeg toegangsbeleid toe**en kies het account waarmee u bent aangemeld als principal.

1. Kies in Visual Studio **Accountinstellingen** > **bestand**.
Selecteer **Een account toevoegen** in de sectie Alle **account.** Meld u aan met het account dat u hebt gekozen als opdrachtgever van uw toegangsbeleid.

1. Kies > **Extra-opties**en zoek naar **Azure Service Authentication**. **Tools** Selecteer vervolgens het account dat u zojuist aan Visual Studio hebt toegevoegd.

Wanneer u uw toepassing debugt, maakt Visual Studio nu verbinding met het account waarop uw Key Vault zich bevindt.

## <a name="how-your-aspnet-core-project-is-modified"></a>Hoe uw ASP.NET Core-project wordt gewijzigd

In deze sectie worden de exacte wijzigingen in een ASP.NET project aangegeven bij het toevoegen van de Key Vault-verbonden service met Behulp van Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Referenties toegevoegd voor ASP.NET Core

Is van invloed op het projectbestand .NET-verwijzingen en NuGet-pakketreferenties.

| Type | Naslaginformatie |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Bestanden toegevoegd voor ASP.NET Core

- `ConnectedService.json`toegevoegd, die bepaalde informatie over de Verbonden leverancier van de Dienst, versie, en een verbinding de documentatie registreert.

### <a name="project-file-changes-for-aspnet-core"></a>Wijzigingen in projectbestand voor ASP.NET Core

- Added the Connected Services `ConnectedServices.json` ItemGroup and file.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings.json wijzigingen voor ASP.NET Core

- De volgende omgevingsvariabele vermeldingen toevoegen aan zowel het IIS Express-profiel als het profiel dat overeenkomt met de naam van uw webproject:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Wijzigingen in Azure voor ASP.NET Core

- Een resourcegroep gemaakt (of een bestaande groep gebruikt).
- Een sleutelkluis gemaakt in de opgegeven resourcegroep.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Hoe uw ASP.NET Framework-project wordt gewijzigd

In deze sectie worden de exacte wijzigingen in een ASP.NET project aangegeven bij het toevoegen van de Key Vault-verbonden service met Behulp van Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Referenties toegevoegd voor ASP.NET Framework

Beïnvloedt het projectbestand .NET-verwijzingen en `packages.config` (NuGet-verwijzingen).

| Type | Naslaginformatie |
| --- | --- |
| .net; NuGet (NuGet) | Microsoft.Azure.KeyVault |
| .net; NuGet (NuGet) | Microsoft.Azure.KeyVault.WebKey |
| .net; NuGet (NuGet) | Microsoft.Rest.ClientRuntime |
| .net; NuGet (NuGet) | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Bestanden toegevoegd voor ASP.NET Framework

- `ConnectedService.json`toegevoegd, die bepaalde informatie over de Verbonden Leverancier van de Dienst, versie, en een verbinding aan de documentatie registreert.

### <a name="project-file-changes-for-aspnet-framework"></a>Wijzigingen in projectbestanden voor ASP.NET Framework

- Added the Connected Services ItemGroup and ConnectedServices.json file.
- Verwijzingen naar de .NET-assemblages die zijn beschreven in de sectie [Toegevoegde verwijzingen.](#added-references-for-aspnet-framework)

### <a name="webconfig-or-appconfig-changes"></a>web.config of app.config wijzigingen

- De volgende configuratievermeldingen toegevoegd:

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure-for-aspnet-framework"></a>Wijzigingen op Azure voor ASP.NET Framework

- Een resourcegroep gemaakt (of een bestaande groep gebruikt).
- Een sleutelkluis gemaakt in de opgegeven resourcegroep.

## <a name="next-steps"></a>Volgende stappen

Als u deze zelfstudie hebt gevolgd, zijn uw Key Vault-machtigingen ingesteld om met uw eigen Azure-abonnement uit te voeren, maar dat is mogelijk niet wenselijk voor een productiescenario. U een beheerde identiteit maken om Key Vault-toegang voor uw app te beheren. Zie [Key Vault-verificatie bieden met een beheerde identiteit](/azure/key-vault/managed-identity).

Lees meer over de ontwikkeling van Key Vault door de [Sleutelkluishandleiding te](key-vault-developers-guide.md)lezen.
