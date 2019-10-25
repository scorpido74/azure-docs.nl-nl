---
title: Zelf studie voor het gebruik van Azure-app configuratie Key Vault verwijzingen in een ASP.NET Core-app | Microsoft Docs
description: In deze zelf studie leert u hoe u Key Vault verwijzingen van Azure-app configuratie kunt gebruiken vanuit een ASP.NET Core-app
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 277333cbca5a31fdc08ae943d2ff61c35d2c9310
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802362"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Zelf studie: Key Vault verwijzingen gebruiken in een ASP.NET Core-app

In deze zelf studie leert u hoe u de Azure-app Configuration-service kunt gebruiken in combi natie met Azure Key Vault. App-configuratie en Key Vault zijn complementaire services die naast elkaar worden gebruikt in de meeste toepassings implementaties.

Met app-configuratie kunt u de services gezamenlijk gebruiken door sleutels te maken die verwijzen naar waarden die zijn opgeslagen in Key Vault. Wanneer de app-configuratie dergelijke sleutels maakt, worden de Uri's van Key Vault waarden opgeslagen in plaats van de waarden zelf.

In uw toepassing wordt gebruikgemaakt van de client provider voor app-configuratie om Key Vault verwijzingen op te halen, net zoals bij andere sleutels die zijn opgeslagen in de app-configuratie. In dit geval zijn de waarden die zijn opgeslagen in de app-configuratie Uri's die verwijzen naar de waarden in de Key Vault. Ze zijn niet Key Vault waarden of referenties. Omdat de client provider de sleutels als Key Vault verwijzingen herkent, wordt Key Vault gebruikt om de waarden op te halen.

Uw toepassing is verantwoordelijk voor het correct verifiëren van de app-configuratie en de Key Vault. De twee services communiceren niet rechtstreeks.

In deze zelf studie wordt uitgelegd hoe u Key Vault verwijzingen in uw code implementeert. Dit is gebaseerd op de web-app die is geïntroduceerd in de quickstarts. Voordat u doorgaat, moet u eerst [een ASP.net core-app maken met de app-configuratie](./quickstart-aspnet-core-app.md) .

U kunt elke code-editor gebruiken om de stappen in deze zelf studie uit te voeren. [Visual Studio code](https://code.visualstudio.com/) is bijvoorbeeld een platformoverschrijdende code-editor die beschikbaar is voor de Windows-, macOS-en Linux-besturings systemen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een app-configuratie sleutel maken die verwijst naar een waarde die is opgeslagen in Key Vault.
> * Toegang tot de waarde van deze sleutel vanuit een ASP.NET Core-webtoepassing.

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelf studie begint, installeert u de [.net core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Een kluis maken

1. Selecteer de optie **een resource maken** in de linkerbovenhoek van de Azure portal:

    ![Uitvoer nadat het maken van de sleutel kluis is voltooid](./media/quickstarts/search-services.png)
1. Voer in het zoekvak **Key Vault**in.
1. Selecteer in de lijst met resultaten de optie **sleutel kluizen** aan de linkerkant.
1. Selecteer **toevoegen**in **sleutel kluizen**.
1. Geef aan de rechter kant in **sleutel kluis maken**de volgende informatie op:
    - Selecteer **abonnement** om een abonnement te kiezen.
    - Selecteer in **resource groep**de optie **nieuwe maken** en voer een naam voor de resource groep in.
    - In de naam van de **sleutel kluis**is een unieke naam vereist. Voer voor deze zelf studie **Contoso-vault2**in.
    - Kies een locatie in de vervolg keuzelijst **regio** .
1. Wijzig de andere opties voor **sleutel kluis maken** met de standaard waarden.
1. Selecteer **Maken**.

Op dit moment is uw Azure-account het enige dat is gemachtigd voor toegang tot deze nieuwe kluis.

![Uitvoer nadat het maken van de sleutel kluis is voltooid](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Een geheim toevoegen aan Key Vault

Als u een geheim wilt toevoegen aan de kluis, hoeft u slechts een paar extra stappen uit te voeren. In dit geval voegt u een bericht toe dat u kunt gebruiken om Key Vault ophalen te testen. Het bericht wordt **bericht**genoemd en u slaat de waarde ' Hello from key Vault ' op.

1. Selecteer op de pagina eigenschappen van Key Vault de optie **geheimen**.
1. Selecteer **genereren/importeren**.
1. Voer in het deel venster **een geheim maken** de volgende waarden in:
    - **Opties voor uploaden**: Voer **hand matig**in.
    - **Naam**: Voer een **bericht**in.
    - **Waarde**: Voer een **Hello van Key Vault in**.
1. Zorg ervoor dat u de andere **geheime eigenschappen maakt** met de standaard waarden.
1. Selecteer **Maken**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Een Key Vault verwijzing toevoegen aan de app-configuratie

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **alle resources**en selecteer vervolgens de app-configuratie Store-instantie die u hebt gemaakt in de Quick Start.

1. Selecteer **Configuration Explorer**.

1. Selecteer **+**  > **sleutel kluis referentie**maken en geef vervolgens de volgende waarden op:
    - **Sleutel**: Select **TestApp: Settings: KeyVaultMessage**.
    - **Label**: laat deze waarde leeg.
    - **Abonnement**, **resource groep**en **sleutel kluis**: Voer de waarden in die overeenkomen met die in de sleutel kluis die u hebt gemaakt in de vorige sectie.
    - **Geheim**: Selecteer het geheime **bericht** dat u in de vorige sectie hebt gemaakt.

## <a name="connect-to-key-vault"></a>Verbinding maken met Key Vault

1. In deze zelf studie gebruikt u een service-principal voor verificatie om Key Vault. Als u deze service-principal wilt maken, gebruikt u de opdracht Azure CLI [AZ AD SP create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Met deze bewerking wordt een reeks sleutel/waarde-paren geretourneerd:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Voer de volgende opdracht uit om de Service-Principal toegang te geven tot uw sleutel kluis:

    ```
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Voeg in de volgende opdrachten geheimen toe in plaats van *clientId* en *ClientSecret* naar geheimen Manager. De opdrachten moeten worden uitgevoerd in dezelfde map als het *. csproj* -bestand.

    ```
    dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>
    dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>
    ```

> [!NOTE]
> Deze Key Vault referenties worden alleen in uw toepassing gebruikt. Uw toepassing verifieert rechtstreeks aan Key Vault met deze referenties. Ze worden nooit door gegeven aan de app-configuratie service.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Uw code bijwerken om een Key Vault referentie te gebruiken

1. Open *Program.cs*en voeg verwijzingen toe aan de volgende vereiste pakketten:

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Werk de `CreateWebHostBuilder`-methode bij om app-configuratie te gebruiken door de `config.AddAzureAppConfiguration`-methode aan te roepen. Neem de `UseAzureKeyVault` optie op voor het door geven van een nieuwe `KeyVaultClient` verwijzing naar uw Key Vault.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                KeyVaultClient kvClient = new KeyVaultClient(async (authority, resource, scope) =>
                {
                    var adCredential = new ClientCredential(settings["ConnectionStrings:KeyVaultClientId"], settings["ConnectionStrings:KeyVaultClientSecret"]);
                    var authenticationContext = new AuthenticationContext(authority, null);
                    return (await authenticationContext.AcquireTokenAsync(resource, adCredential)).AccessToken;
                });

                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .UseAzureKeyVault(kvClient); });
            })
            .UseStartup<Startup>();
    ```

1. Wanneer u de verbinding met de app-configuratie hebt geïnitialiseerd, hebt u de `KeyVaultClient` verwijzing naar de `UseAzureKeyVault`-methode door gegeven. Na de initialisatie kunt u de waarden van Key Vault verwijzingen op dezelfde manier openen als de waarden van reguliere app-configuratie sleutels.

    Als u dit proces in actie wilt zien, opent u *index. cshtml* in de **weer gaven** > **basismap** . Vervang de inhoud door de volgende code:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    U hebt toegang tot de waarde van de Key Vault Reference **TestApp: Settings: KeyVaultMessage** op dezelfde manier als voor de configuratie waarde van **TestApp: Settings: Message**.

## <a name="build-and-run-the-app-locally"></a>De app lokaal bouwen en uitvoeren

1. Als u de app wilt bouwen met behulp van de .NET Core SLI, voert u de volgende opdracht uit in de opdracht shell:

    ```
    dotnet build
    ```

1. Wanneer de build is voltooid, gebruikt u de volgende opdracht om de web-app lokaal uit te voeren:

    ```
    dotnet run
    ```

1. Open een browser venster en ga naar `http://localhost:5000`. Dit is de standaard-URL voor de web-app die lokaal wordt gehost.

    ![Starten van lokale app voor Quick Start](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een app-configuratie sleutel gemaakt die verwijst naar een waarde die is opgeslagen in Key Vault. Ga door naar de volgende zelf studie voor meer informatie over het toevoegen van een door Azure beheerde service-identiteit die de toegang tot de app-configuratie en Key Vault stroomlijnt.

> [!div class="nextstepaction"]
> [Beheerde identiteits integratie](./howto-integrate-azure-managed-service-identity.md)
