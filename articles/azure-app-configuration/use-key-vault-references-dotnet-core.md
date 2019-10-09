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
ms.openlocfilehash: a14cb3035c159c82df44f686da7f7b78ef942943
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035856"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Zelfstudie: Key Vault verwijzingen gebruiken in een ASP.NET Core-app

In deze zelf studie leert u hoe u de Azure-app Configuration-service kunt gebruiken in combi natie met Azure Key Vault. Dit zijn complementaire Services, die naast elkaar worden gebruikt in de meeste toepassings implementaties. Om u te helpen ze samen te gebruiken, kunt u met app-configuratie sleutels maken die verwijzen naar waarden die zijn opgeslagen in Key Vault. Wanneer u dit doet, slaat de app-configuratie de URI op in de Key Vault waarde in plaats van de waarde zelf. Uw toepassing haalt de waarde van deze sleutel op met behulp van de app Configuration client provider, net zoals elke andere sleutel die in de app-configuratie is opgeslagen. De client provider herkent deze als een Key Vault-verwijzing en roept Key Vault om de waarde op te halen. Uw toepassing is verantwoordelijk voor het correct verifiëren van de app-configuratie en de Key Vault. De twee services communiceren niet rechtstreeks.

In deze zelf studie ziet u hoe u Key Vault verwijzingen in uw code kunt implementeren. Dit is gebaseerd op de web-app die is geïntroduceerd in de quickstarts. Voordat u doorgaat, moet u eerst [een ASP.net core-app maken met de app-configuratie](./quickstart-aspnet-core-app.md) .

U kunt elke code-editor gebruiken om de stappen in deze zelf studie uit te voeren. [Visual Studio code](https://code.visualstudio.com/) is een uitstekende optie die beschikbaar is op de Windows-, macOS-en Linux-platformen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een app-configuratie sleutel maken die verwijst naar een waarde die is opgeslagen in Key Vault
> * Toegang krijgen tot de waarde van deze sleutel vanuit een ASP.NET Core-webtoepassing

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt uitvoeren, installeert u de [.net core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Een kluis maken

1. Selecteer de optie **Een resource maken** in de linkerbovenhoek van Azure Portal

    ![Uitvoer nadat het maken van de sleutelkluis is voltooid](./media/quickstarts/search-services.png)
2. Typ **Sleutelkluis** in het zoekvak.
3. Kies **Sleutelkluis** in de lijst met resultaten.
4. Kies **Maken** in de sectie Sleutelkluis.
5. Geef in de sectie **Sleutelkluis maken** de volgende gegevens op:
    - **Naam**: geef een unieke naam op. Voor deze Quick Start gebruiken we **Contoso-vault2**. 
    - **Abonnement**: Kies een abonnement.
    - Kies onder **resource groep**de optie **nieuwe maken** en voer een naam voor de resource groep in.
    - Kies een locatie in de vervolgkeuzelijst **Locatie**.
    - Houd voor de overige opties de standaardwaarden aan.
6. Selecteer na het opgeven van de bovenstaande gegevens **Maken**.

Op dit moment is uw Azure-account het enige dat is gemachtigd voor toegang tot deze nieuwe kluis.

![Uitvoer nadat het maken van de sleutelkluis is voltooid](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Een geheim toevoegen aan Key Vault

Als u een geheim wilt toevoegen aan de kluis, hoeft u maar een paar extra stappen uit te voeren. In dit geval voegen we een bericht toe dat we kunnen gebruiken om Key Vault ophalen te testen. Het bericht wordt **bericht** genoemd en we slaan de waarde van **hallo op uit Key Vault** .

1. Selecteer op de pagina eigenschappen van Key Vault de optie **geheimen**.
1. Klik op **Genereren/importeren**.
1. Kies in het scherm **Een geheim maken** de volgende waarden:
    - **Uploadopties**: Handmatig.
    - **Naam**: Message
    - **Waarde**: Hallo van Key Vault
    - Houd voor de overige waarden de standaardwaarden aan. Klik op **Create**.

## <a name="add-a-key-vault-reference-to-app-config"></a>Een Key Vault verwijzing toevoegen aan de app-configuratie

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **alle resources**en selecteer de app-configuratie Store-instantie die u hebt gemaakt in de Quick Start.

1. Klik op **configuratie Verkenner**

1. Klik op **+ maken** > **sleutel kluis referentie** en kies de volgende waarden:
    - **Sleutel**: TestApp: instellingen: KeyVaultMessage
    - **Label**: Leeg laten
    - **Abonnement**, **resource groep**, **sleutel kluis**: Kies de opties die overeenkomen met het Key Vault dat u in de vorige sectie hebt gemaakt.
    - **Geheim**: Selecteer het geheime **bericht** dat u in de vorige sectie hebt gemaakt.

## <a name="connect-to-key-vault"></a>Verbinding maken met Key Vault

1. Voor deze zelf studie gebruikt u een service-principal voor verificatie bij de sleutel kluis. Als u deze service-principal wilt maken, gebruikt u de opdracht Azure CLI [AZ AD SP create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Met deze bewerking wordt een reeks sleutel-waardeparen geretourneerd. 

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

        az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey

1. Geheimen voor *clientId* en *ClientSecret* toevoegen aan geheimen Manager. Deze opdrachten moeten worden uitgevoerd in dezelfde map als het *. csproj* -bestand.

        dotnet user-secrets set ConnectionStrings:KeyVaultClientId <clientId-of-your-service-principal>        
        dotnet user-secrets set ConnectionStrings:KeyVaultClientSecret <clientSecret-of-your-service-principal>

> [!NOTE]
> Deze Key Vault referenties worden alleen in uw toepassing gebruikt. Uw toepassing verifieert rechtstreeks aan Key Vault met deze referenties. Ze worden nooit door gegeven aan de app-configuratie service.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Uw code bijwerken om een Key Vault referentie te gebruiken

1. Open *Program.cs*en voeg verwijzingen toe aan de vereiste pakketten.

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Werk de `CreateWebHostBuilder`-methode bij om app-configuratie te gebruiken door de `config.AddAzureAppConfiguration()`-methode aan te roepen. Neem de `UseAzureKeyVault` optie op, waarbij u een nieuwe `KeyVaultClient`-verwijzing naar uw Key Vault doorgeeft.

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

1. Wanneer u de *KeyVaultClient* -verwijzing naar de `UseAzureKeyVault`-methode hebt door lopen bij het initialiseren van de verbinding met de app-configuratie, hebt u toegang tot de waarden van Key Vault verwijzingen op dezelfde manier als u de waarden van reguliere app-configuratie sleutels opent. Als u dit proces in actie wilt zien, opent u *index. cshtml* in de weer gaven > basismap. Vervang de inhoud door de volgende code:

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

    U hebt toegang tot de waarde van de Key Vault Reference *TestApp: Settings: KeyVaultMessage* op dezelfde manier als de configuratie waarde *TestApp: Settings: Message*

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Als u de app wilt bouwen met behulp van de .NET Core SLI, voert u de volgende opdracht uit in de opdracht shell:

        dotnet build

2. Wanneer de build is voltooid, voert u de volgende opdracht uit om de web-app lokaal uit te voeren:

        dotnet run

3. Open een browser venster en ga naar `http://localhost:5000`. Dit is de standaard-URL voor de web-app die lokaal wordt gehost.

    ![Quickstart voor het lokaal starten van een app](./media/key-vault-reference-launch-local.png)


## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een beheerde Azure-service-identiteit toegevoegd om toegang tot app-configuratie te stroomlijnen en het beheer van referenties voor uw app te verbeteren. Ga verder met de voor beelden van Azure CLI voor meer informatie over het gebruik van app-configuratie.

> [!div class="nextstepaction"]
> [CLI-voorbeelden](./cli-samples.md)
