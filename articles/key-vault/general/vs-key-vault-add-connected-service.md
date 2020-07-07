---
title: Key Vault ondersteuning toevoegen aan uw ASP.NET-project met behulp van Visual Studio-Azure Key Vault | Microsoft Docs
description: Gebruik deze zelf studie om te leren hoe u Key Vault ondersteuning kunt toevoegen aan een ASP.NET-of ASP.NET Core-webtoepassing.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: af0065db087595167ca71bb79b968cc4ad339acd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82116839"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Key Vault toevoegen aan uw web-app met behulp van Visual Studio Connected Services

In deze zelf studie leert u hoe u eenvoudig alles kunt toevoegen wat u nodig hebt om Azure Key Vault te gaan gebruiken voor het beheren van uw geheimen voor webprojecten in Visual Studio, ongeacht of u ASP.NET Core of een wille keurig type ASP.NET-project gebruikt. Met de functie voor verbonden services in Visual Studio kunt u Visual Studio automatisch alle NuGet-pakketten en configuratie-instellingen laten toevoegen die u nodig hebt om verbinding te maken met Key Vault in Azure.

Voor meer informatie over de wijzigingen die verbonden services in uw project maken om Key Vault in te scha kelen, raadpleegt u [Key Vault verbonden service: wat is er gebeurd met mijn ASP.net 4.7.1-project](#how-your-aspnet-framework-project-is-modified) of [Key Vault verbonden service: wat is er gebeurd met mijn ASP.net core-project](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Vereisten

- **Een Azure-abonnement**. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/pricing/free-trial/).
- **Visual studio 2019 versie 16,3** of hoger, of **Visual studio 2017 versie 15,7** met de werk belasting voor **Web Development** geïnstalleerd. [Download nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Voor ASP.NET (niet kern geheugens) met Visual Studio 2017 hebt u de ontwikkel Hulpprogramma's van .NET Framework 4.7.1 of hoger nodig, die niet standaard worden geïnstalleerd. Als u deze wilt installeren, opent u het installatie programma van Visual Studio, kiest u **wijzigen**en kiest u vervolgens **afzonderlijke onderdelen**. Vouw vervolgens aan de rechter kant **ASP.net en web development**uit en kies **.NET Framework 4.7.1 Development tools**.
- Een ASP.NET-4.7.1 of hoger, of ASP.NET Core 2,0 of hoger web-project geopend.

## <a name="add-key-vault-support-to-your-project"></a>Key Vault ondersteuning toevoegen aan uw project

Voordat u begint, moet u ervoor zorgen dat u bent aangemeld bij Visual Studio. Meld u aan met hetzelfde account dat u gebruikt voor uw Azure-abonnement. Open vervolgens een ASP.NET-4.7.1 of hoger of ASP.NET Core 2,0-webproject en voer de volgende stappen uit:

1. Klik in **Solution Explorer**met de rechter muisknop op het project waaraan u de Key Vault ondersteuning wilt toevoegen en kies **Add**  >  **verbonden service**toevoegen.
   De Connected Service-pagina wordt weergegeven met services die u aan uw project kunt toevoegen.
1. Kies in het menu met beschik bare Services **beveiligde geheimen met Azure Key Vault**.

   ![Kies beveiligde geheimen met Azure Key Vault](../media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Selecteer het abonnement dat u wilt gebruiken en kies vervolgens een nieuwe of bestaande Key Vault. Als u de nieuwe Key Vault selecteert, wordt een koppeling **bewerken** weer gegeven. Selecteer deze optie om uw nieuwe Key Vault te configureren.

   ![Uw abonnement selecteren](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. In **Azure Key Vault bewerken**voert u de naam in die u wilt gebruiken voor de Key Vault.

1. Selecteer een bestaande **resource groep**of kies ervoor om een nieuwe te maken met een automatisch gegenereerde unieke naam.  Als u een nieuwe groep met een andere naam wilt maken, kunt u de [Azure Portal](https://portal.azure.com)gebruiken, en vervolgens de pagina sluiten en opnieuw opstarten om de lijst met resource groepen opnieuw te laden.
1. Kies de **locatie** waar u de Key Vault wilt maken. Als uw webtoepassing wordt gehost in azure, kiest u de regio die als host fungeert voor de webtoepassing voor optimale prestaties.
1. Kies een **prijs categorie**. Zie [Key Vault prijzen](https://azure.microsoft.com/pricing/details/key-vault/)voor meer informatie.
1. Kies **OK** om de configuratie opties te accepteren.
1. Nadat u een bestaand Key Vault hebt geselecteerd of een nieuwe Key Vault hebt geconfigureerd, selecteert u op het tabblad **Azure Key Vault** van Visual Studio **toevoegen** om de verbonden service toe te voegen.
1. Selecteer de koppeling **geheimen beheren die zijn opgeslagen op deze key Vault** om de pagina **geheimen** te openen voor uw Key Vault. Als u de pagina of het project hebt gesloten, kunt u ernaar navigeren in het [Azure Portal](https://portal.azure.com) door **alle services** te kiezen en vervolgens onder **beveiliging**te kiezen **Key Vault**en vervolgens uw Key Vault te kiezen.
1. Kies in het gedeelte Key Vault voor de Key Vault die u hebt gemaakt, de optie **geheimen**en **Genereer/Importeer**vervolgens.

   ![Een geheim genereren/importeren](../media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Voer een geheim in, zoals *MySecret* en geef het een wille keurige teken reeks waarde als test en selecteer vervolgens de knop **maken** .

   ![Een geheim maken](../media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. Beschrijving Geef een ander geheim op, maar deze keer wordt in een categorie geplaatst door de naam geheimen te noemen *--MySecret*. Met deze syntaxis geeft u een categorie geheimen op die een geheim ' MySecret ' bevat.

Nu kunt u toegang krijgen tot uw geheimen in code. De volgende stappen verschillen, afhankelijk van of u ASP.NET 4.7.1 of ASP.NET Core gebruikt.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Toegang tot uw geheimen in code (ASP.NET Core)

1. Klik in Solution Explorer met de rechter muisknop op uw project en selecteer **NuGet-pakketten beheren**. Op het tabblad **Bladeren** zoekt en installeert u deze twee NuGet-pakketten: [micro soft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) en voor .net Core 2, voegt u [micro soft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) de sleutel kluis of voor .net Core 3 toe. Voeg[micro soft. Azure.-kluis. core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core)toe.

1. Voor .NET Core 2 selecteert u het `Program.cs` tabblad en wijzigt `BuildWebHost` u de definitie in de programma klasse in het volgende:

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

   Gebruik de volgende code voor .NET Core 3.

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

1. Open vervolgens een van de pagina bestanden, zoals *index.cshtml.cs* en schrijf de volgende code:
   1. Voeg een verwijzing toe naar met `Microsoft.Extensions.Configuration` behulp van de instructie:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Voeg de configuratie variabele toe.

      ```csharp
      private static IConfiguration _configuration;
      ```

   1. Voeg deze constructor toe of vervang de bestaande constructor door de volgende:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Werk de `OnGet`-methode bij. Werk de waarde van de tijdelijke aanduiding die hier wordt weer gegeven bij met de geheime naam die u in de bovenstaande opdrachten hebt gemaakt.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. Als u de waarde tijdens runtime wilt bevestigen, voegt u code toe om weer te geven `ViewData["Message"]` in het *cshtml* -bestand om het geheim in een bericht weer te geven.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

U kunt de app lokaal uitvoeren om te controleren of het geheim van de Key Vault is opgehaald.

## <a name="access-your-secrets-aspnet"></a>Toegang tot uw geheimen (ASP.NET)

U kunt de configuratie zo instellen dat het web.config-bestand een dummy waarde heeft in het `appSettings` element dat tijdens runtime wordt vervangen door de waarde waar. U kunt dit vervolgens openen via de `ConfigurationManager.AppSettings` gegevens structuur.

1. Bewerk het web.config-bestand.  Zoek de tag appSettings, voeg een kenmerk toe `configBuilders="AzureKeyVault"` en voeg een regel toe:

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. Bewerk de `About` methode in *HomeController.cs*om de waarde voor de bevestiging weer te geven.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. Voer de app lokaal uit onder het fout opsporingsprogramma, schakel over naar het tabblad **about** en controleer of de waarde van de Key Vault wordt weer gegeven.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep als u deze niet meer nodig hebt. Hiermee worden de Key Vault en gerelateerde resources verwijderd. De resourcegroep verwijderen via de portal:

1. Voer de naam van uw resourcegroep in het vak Zoeken bovenaan de portal in. Wanneer u de in deze snelstart gebruikte resourcegroep in de zoekresultaten ziet, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Voer in het vak **Typ de naam van de resource groep:** de naam van de resource groep in en selecteer **verwijderen**.

## <a name="troubleshooting"></a>Problemen oplossen

Als uw Key Vault wordt uitgevoerd op een ander Microsoft-account dan het account dat u hebt aangemeld bij Visual Studio (bijvoorbeeld wanneer de Key Vault wordt uitgevoerd op uw eigen account, maar Visual Studio gebruikmaakt van uw persoonlijke accounts), krijgt u een fout in uw Program.cs-bestand, dat Visual Studio geen toegang kan krijgen tot de Key Vault. Dit probleem oplossen:

1. Ga naar de [Azure Portal](https://portal.azure.com) en open uw Key Vault.

1. Kies **toegangs beleid**, vervolgens **toegangs beleid toevoegen**en kies het account waarmee u bent aangemeld als principal.

1. Kies in Visual Studio de instellingen voor het **Bestands**  >  **account**.
Selecteer **een account toevoegen** in het gedeelte **alle accounts** . Meld u aan met het account dat u hebt gekozen als principal van uw toegangs beleid.

1. Kies **extra**  >  **Opties**en zoek naar **Azure-service verificatie**. Selecteer vervolgens het account dat u zojuist hebt toegevoegd aan Visual Studio.

Wanneer u nu fouten opspoort in uw toepassing, maakt Visual Studio verbinding met het account waarop uw Key Vault zich bevindt.

## <a name="how-your-aspnet-core-project-is-modified"></a>Hoe uw ASP.NET Core project wordt gewijzigd

In deze sectie worden de exacte wijzigingen in een ASP.NET-project geïdentificeerd wanneer u de Key Vault verbonden service toevoegt met behulp van Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Er zijn verwijzingen toegevoegd voor ASP.NET Core

Van invloed op het project bestand .NET-verwijzingen en NuGet-pakket verwijzingen.

| Type | Naslaginformatie |
| --- | --- |
| NuGet | Micro soft. AspNetCore. AzureKeyVault. HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Toegevoegde bestanden voor ASP.NET Core

- `ConnectedService.json`toegevoegd, waarmee een aantal informatie over de verbonden service provider, de versie en een koppeling naar de documentatie worden vastgelegd.

### <a name="project-file-changes-for-aspnet-core"></a>Wijzigingen in het project bestand voor ASP.NET Core

- Het ItemGroup en het bestand van de verbonden services zijn toegevoegd `ConnectedServices.json` .

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings.jswijzigingen voor ASP.NET Core

- De volgende omgevings variabele-vermeldingen zijn toegevoegd aan het IIS Express profiel en het profiel dat overeenkomt met de naam van uw webproject:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Wijzigingen in azure voor ASP.NET Core

- Een resource groep gemaakt (of een bestaande gebruikt).
- Er is een Key Vault gemaakt in de opgegeven resource groep.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Hoe uw ASP.NET Framework-project wordt gewijzigd

In deze sectie worden de exacte wijzigingen in een ASP.NET-project geïdentificeerd wanneer u de Key Vault verbonden service toevoegt met behulp van Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Er zijn verwijzingen toegevoegd voor ASP.NET Framework

Heeft betrekking op het project bestand .NET `packages.config` -verwijzingen en (NuGet references).

| Type | Naslaginformatie |
| --- | --- |
| NET NuGet | Microsoft.Azure.KeyVault |
| NET NuGet | Micro soft. Azure. WebKey |
| NET NuGet | Microsoft.Rest.ClientRuntime |
| NET NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Bestanden toegevoegd voor ASP.NET Framework

- `ConnectedService.json`toegevoegd, waarmee een aantal informatie over de verbonden service provider, de versie en een koppeling naar de documentatie wordt vastgelegd.

### <a name="project-file-changes-for-aspnet-framework"></a>Wijzigingen in het project bestand voor ASP.NET Framework

- De verbonden services ItemGroup en ConnectedServices.jstoegevoegd aan het bestand.
- Verwijzingen naar de .NET-assembly's die worden beschreven in de sectie [toegevoegde verwijzingen](#added-references-for-aspnet-framework) .

### <a name="webconfig-or-appconfig-changes"></a>Wijzigingen web.config of app.config

- De volgende configuratie vermeldingen zijn toegevoegd:

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

### <a name="changes-on-azure-for-aspnet-framework"></a>Wijzigingen in azure voor ASP.NET Framework

- Een resource groep gemaakt (of een bestaande gebruikt).
- Er is een Key Vault gemaakt in de opgegeven resource groep.

## <a name="next-steps"></a>Volgende stappen

Als u deze zelf studie hebt gevolgd, worden uw Key Vault machtigingen ingesteld om te worden uitgevoerd met uw eigen Azure-abonnement, maar dit is mogelijk niet wenselijk voor een productie scenario. U kunt een beheerde identiteit maken om Key Vault toegang voor uw app te beheren. Zie [Key Vault verificatie bieden met een beheerde identiteit](/azure/key-vault/managed-identity).

Lees de [Key Vault ontwikkelaars handleiding](developers-guide.md)voor meer informatie over het ontwikkelen van Key Vault.
