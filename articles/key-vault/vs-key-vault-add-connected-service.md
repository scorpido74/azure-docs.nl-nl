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
ms.openlocfilehash: 4cbc4044b5d1270cecd1a271d2a1db02801650dd
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012775"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Key Vault toevoegen aan uw web-app met behulp van Visual Studio Connected Services

In deze zelf studie leert u hoe u eenvoudig alles kunt toevoegen wat u nodig hebt om Azure Key Vault te gaan gebruiken voor het beheren van uw geheimen voor webprojecten in Visual Studio, ongeacht of u ASP.NET Core of een wille keurig type ASP.NET-project gebruikt. Met de functie voor verbonden services in Visual Studio kunt u Visual Studio automatisch alle NuGet-pakketten en configuratie-instellingen laten toevoegen die u nodig hebt om verbinding te maken met Key Vault in Azure.

Voor meer informatie over de wijzigingen die verbonden services in uw project maken om Key Vault in te scha kelen, raadpleegt u [Key Vault verbonden service: wat is er gebeurd met mijn ASP.net 4.7.1-project](#how-your-aspnet-framework-project-is-modified) of [Key Vault verbonden service: wat is er gebeurd met mijn ASP.net core-project](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Vereisten

- **Een Azure-abonnement**. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/pricing/free-trial/).
- **Visual studio 2019 versie 16,3 Preview 1** of hoger, of **Visual Studio 2017 versie 15,7** met de **Web Development** -werk belasting geïnstalleerd. [Nu downloaden](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).
- Voor ASP.NET (niet kern geheugens) met Visual Studio 2017 hebt u de ontwikkel Hulpprogramma's van .NET Framework 4.7.1 of hoger nodig, die niet standaard worden geïnstalleerd. Als u deze wilt installeren, start u het installatie programma van Visual Studio, kiest u **wijzigen**en kiest u vervolgens **afzonderlijke onderdelen**. Vouw vervolgens aan de rechter kant **ASP.net en web development**uit en kies **.NET Framework 4.7.1 Development tools** .
- Een ASP.NET 4.7.1 of hoger, of ASP.NET Core 2,0-webproject geopend.

## <a name="add-key-vault-support-to-your-project"></a>Key Vault ondersteuning toevoegen aan uw project

Voordat u begint, moet u ervoor zorgen dat u bent aangemeld bij Visual Studio. Meld u aan met hetzelfde account dat u gebruikt voor uw Azure-abonnement. Open vervolgens een ASP.NET-4.7.1 of hoger of ASP.NET Core 2,0-webproject en voer de volgende stappen uit:

1. Klik in **Solution Explorer**met de rechter muisknop op het project waaraan u de sleutel kluis ondersteuning wilt toevoegen en kies > **verbonden service** **toevoegen** .
   De Connected Service-pagina wordt weergegeven met services die u aan uw project kunt toevoegen.
1. Kies in het menu met beschik bare Services **beveiligde geheimen met Azure Key Vault**.

   ![Kies beveiligde geheimen met Azure Key Vault](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. Selecteer het abonnement dat u wilt gebruiken en kies vervolgens een nieuwe of bestaande Key Vault. Als u de nieuwe Key Vault selecteert, wordt een koppeling **bewerken** weer gegeven. Selecteer deze optie om uw nieuwe Key Vault te configureren.

   ![Selecteer uw abonnement](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. In **Azure Key Vault bewerken**voert u de naam in die u wilt gebruiken voor de Key Vault.

1. Selecteer een bestaande **resource groep**of kies ervoor om een nieuwe te maken met een automatisch gegenereerde unieke naam.  Als u een nieuwe groep met een andere naam wilt maken, kunt u de [Azure Portal](https://portal.azure.com)gebruiken, en vervolgens de pagina sluiten en opnieuw opstarten om de lijst met resource groepen opnieuw te laden.
1. Kies de **locatie** waar u de Key Vault wilt maken. Als uw webtoepassing wordt gehost in azure, kiest u de regio die als host fungeert voor de webtoepassing voor optimale prestaties.
1. Kies een **prijs categorie**. Zie [Key Vault prijzen](https://azure.microsoft.com/pricing/details/key-vault/)voor meer informatie.
1. Kies **OK** om de configuratie opties te accepteren.
1. Nadat u een bestaand Key Vault hebt geselecteerd of een nieuwe Key Vault hebt geconfigureerd, selecteert u op het tabblad **Azure Key Vault** van Visual Studio **toevoegen** om de verbonden service toe te voegen.
1. Selecteer de koppeling **geheimen beheren die zijn opgeslagen op deze key Vault** om de pagina **geheimen** te openen voor uw Key Vault. Als u de pagina of het project hebt gesloten, kunt u ernaar navigeren in het [Azure Portal](https://portal.azure.com) door **alle services** te kiezen en vervolgens onder **beveiliging**te kiezen **Key Vault**en vervolgens uw Key Vault te kiezen.
1. In de sectie Key Vault voor de sleutel kluis die u hebt gemaakt, kiest u **geheimen**en vervolgens **genereren/importeren**.

   ![Een geheim genereren/importeren](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. Voer een geheim in, zoals *MySecret* en geef het een wille keurige teken reeks waarde als test en selecteer vervolgens de knop **maken** .

   ![Een geheim maken](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. Beschrijving Geef een ander geheim op, maar deze keer wordt in een categorie geplaatst door de naam geheimen te noemen *--MySecret*. Met deze syntaxis geeft u een categorie geheimen op die een geheim ' MySecret ' bevat.

Nu kunt u toegang krijgen tot uw geheimen in code. De volgende stappen verschillen, afhankelijk van of u ASP.NET 4.7.1 of ASP.NET Core gebruikt.

## <a name="access-your-secrets-in-code"></a>Toegang tot uw geheimen in code

1. Klik in Solution Explorer met de rechter muisknop op uw project en selecteer **NuGet-pakketten beheren**. Op het tabblad **Bladeren** zoekt en installeert u deze twee NuGet-pakketten: [micro soft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) en [micro soft. Azure. sleutel kluis](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

1. Selecteer het tabblad `Program.cs` en vervang de programma klasse door de volgende code:

   ```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
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
            ).UseStartup<Startup>()
             .Build();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

1. Open vervolgens `About.cshtml.cs` bestand en schrijf de volgende code:
   1. Voeg een verwijzing naar `Microsoft.Extensions.Configuration` toe met behulp van de volgende instructie:

       ```csharp
       using Microsoft.Extensions.Configuration
       ```

   1. Deze constructor toevoegen:

       ```csharp
       public AboutModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Werk de `OnGet`-methode bij. Werk de waarde van de tijdelijke aanduiding die hier wordt weer gegeven bij met de geheime naam die u in de bovenstaande opdrachten hebt gemaakt.

       ```csharp
       public void OnGet()
       {
           //Message = "Your application description page.";
           Message = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

Voer de app lokaal uit door te bladeren naar de pagina over. U ziet nu dat de geheime waarde is opgehaald.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep als u deze niet meer nodig hebt. Hiermee worden de Key Vault en gerelateerde resources verwijderd. De resourcegroep verwijderen via de portal:

1. Voer de naam van uw resourcegroep in het vak Zoeken bovenaan de portal in. Wanneer u de in deze snelstart gebruikte resourcegroep in de zoekresultaten ziet, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Voer in het vak **Typ de naam van de resource groep:** de naam van de resource groep in en selecteer **verwijderen**.

## <a name="troubleshooting"></a>Problemen oplossen

Als uw sleutel kluis wordt uitgevoerd op een andere Microsoft-account dan die waarvoor u bent aangemeld bij Visual Studio (de sleutel kluis wordt uitgevoerd op uw werk account, maar Visual Studio gebruikt uw privé-account), krijgt u een fout in uw Program.cs-bestand , dat Visual Studio geen toegang kan krijgen tot de sleutel kluis. Dit probleem oplossen:

1. Ga naar de [Azure Portal](https://portal.azure.com) en open uw Key Vault.

1. Kies **toegangs beleid**, vervolgens **toegangs beleid toevoegen**en kies het account waarmee u bent aangemeld als principal.

1. Kies in Visual Studio de instellingen voor **bestands** > **accounts**.
Selecteer **een account toevoegen** in het gedeelte **alle accounts** . Meld u aan met het account dat u hebt gekozen als principal van uw toegangs beleid.

1. Kies **extra** > **Opties**en zoek naar **Azure-service verificatie**. Selecteer vervolgens het account dat u zojuist hebt toegevoegd aan Visual Studio.

Wanneer u nu fouten opspoort in uw toepassing, maakt Visual Studio verbinding met het account waarop uw sleutel kluis zich bevindt.

## <a name="how-your-aspnet-core-project-is-modified"></a>Hoe uw ASP.NET Core project wordt gewijzigd

In deze sectie worden de exacte wijzigingen in een ASP.NET-project geïdentificeerd wanneer u de Key Vault verbonden service toevoegt met behulp van Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Er zijn verwijzingen toegevoegd voor ASP.NET Core

Van invloed op het project bestand .NET-verwijzingen en NuGet-pakket verwijzingen.

| Type | Naslaginformatie |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Toegevoegde bestanden voor ASP.NET Core

- `ConnectedService.json` toegevoegd, waarmee gegevens over de verbonden service provider, de versie en een koppeling naar de documentatie worden vastgelegd.

### <a name="project-file-changes-for-aspnet-core"></a>Wijzigingen in het project bestand voor ASP.NET Core

- Het ItemGroup-en `ConnectedServices.json`-bestand van de verbonden services zijn toegevoegd.

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>wijzigingen in launchsettings. json voor ASP.NET Core

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

Heeft betrekking op het project bestand .NET-verwijzingen en `packages.config` (NuGet references).

| Type | Naslaginformatie |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>Bestanden toegevoegd voor ASP.NET Framework

- `ConnectedService.json` toegevoegd, waarmee gegevens over de verbonden service provider, de versie en een koppeling naar de documentatie worden vastgelegd.

### <a name="project-file-changes-for-aspnet-framework"></a>Wijzigingen in het project bestand voor ASP.NET Framework

- Het Connected Services ItemGroup-en ConnectedServices. JSON-bestand zijn toegevoegd.
- Verwijzingen naar de .NET-assembly's die worden beschreven in de sectie [toegevoegde verwijzingen](#added-references-for-aspnet-framework) .

### <a name="webconfig-or-appconfig-changes"></a>wijzigingen in web. config of app. config

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

Lees de [Key Vault ontwikkelaars handleiding](key-vault-developers-guide.md)voor meer informatie over het ontwikkelen van Key Vault.
