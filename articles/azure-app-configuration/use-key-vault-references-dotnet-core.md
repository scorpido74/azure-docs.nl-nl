---
title: Zelfstudie voor het gebruik van Verwijzingen naar azure-app-configuratiesleutelvault in een ASP.NET Core-app | Microsoft Documenten
description: In deze zelfstudie leert u hoe u de Key Vault-referenties van Azure App Configuration gebruiken vanuit een ASP.NET Core-app
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
ms.date: 01/21/2020
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: eceb4a9d4e0cc84166280f30b094b82088f53a4a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79475303"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>Zelfstudie: Key Vault-verwijzingen gebruiken in een ASP.NET Core-app

In deze zelfstudie leert u hoe u de Azure App Configuration-service samen met Azure Key Vault gebruiken. App Configuration en Key Vault zijn aanvullende services die naast elkaar worden gebruikt in de meeste implementaties van toepassingen.

App-configuratie helpt u de services samen te gebruiken door sleutels te maken die verwijzen naar waarden die zijn opgeslagen in Key Vault. Wanneer app-configuratie dergelijke sleutels maakt, worden de URI's van key vault-waarden opgeslagen in plaats van de waarden zelf.

Uw toepassing gebruikt de clientprovider App-configuratie om Key Vault-verwijzingen op te halen, net als voor andere sleutels die zijn opgeslagen in app-configuratie. In dit geval zijn de waarden die zijn opgeslagen in app-configuratie URI's die verwijzen naar de waarden in de sleutelkluis. Het zijn geen Key Vault-waarden of referenties. Omdat de clientprovider de sleutels herkent als Key Vault-referenties, gebruikt deze Key Vault om hun waarden op te halen.

Uw toepassing is verantwoordelijk voor het correct verifiëren van zowel app-configuratie als Key Vault. De twee diensten communiceren niet rechtstreeks.

In deze zelfstudie ziet u hoe u Key Vault-verwijzingen in uw code implementeert. Dit is gebaseerd op de web-app die is geïntroduceerd in de quickstarts. Voordat u verdergaat, [voltooit u eerst een ASP.NET Core-app met app-configuratie.](./quickstart-aspnet-core-app.md)

U elke codeeditor gebruiken om de stappen in deze zelfstudie uit te voeren. [Visual Studio Code](https://code.visualstudio.com/) is bijvoorbeeld een cross-platform codeeditor die beschikbaar is voor de Windows-, macOS- en Linux-besturingssystemen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een app-configuratiesleutel die verwijst naar een waarde die is opgeslagen in Key Vault.
> * Toegang tot de waarde van deze sleutel vanuit een ASP.NET Core-webtoepassing.

## <a name="prerequisites"></a>Vereisten

Installeer de [.NET Core SDK](https://dotnet.microsoft.com/download)voordat u deze zelfstudie start.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Een kluis maken

1. Selecteer de optie **Een resource maken** in de linkerbovenhoek van de Azure-portal:

    ![Uitvoer nadat het maken van sleutelkluizen is voltooid](./media/quickstarts/search-services.png)
1. Voer in het zoekvak **Sleutelkluis**in .
1. Selecteer **Sleutelkluizen** aan de linkerkant in de lijst met resultaten.
1. Selecteer Toevoegen **in Sleutelkluizen** **.**
1. Geef aan de rechterkant in **De sleutelkluis maken**de volgende informatie op:
    - Selecteer **Abonnement** om een abonnement te kiezen.
    - Selecteer **in Resourcegroep**De optie **Nieuw maken** en voer een naam van de resourcegroep in.
    - In **Key vault name**is een unieke naam vereist. Voer voor deze zelfstudie **Contoso-vault2**in.
    - Kies in de vervolgkeuzelijst **Regio** een locatie.
1. Laat de andere **opties voor sleutelkluis maken** met hun standaardwaarden.
1. Selecteer **Maken**.

Op dit moment is uw Azure-account de enige die toegang heeft tot deze nieuwe kluis.

![Uitvoer nadat het maken van sleutelkluizen is voltooid](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Een geheim toevoegen aan Key Vault

Om een geheim aan de kluis toe te voegen, moet u slechts een paar extra stappen nemen. Voeg in dit geval een bericht toe dat u gebruiken om Key Vault retrieval te testen. Het bericht heet **Message**en u slaat de waarde 'Hallo van Sleutelkluis' erin op.

1. Selecteer **Geheimen**op de eigenschappenpagina's Van De Sleutelkluis .
1. Selecteer **Genereren/importeren**.
1. Voer in het deelvenster **Een geheim maken** de volgende waarden in:
    - **Uploadopties:** Voer **Handleiding**in .
    - **Naam:** Voer **bericht**in .
    - **Waarde:** Voer **Hallo in vanuit Key Vault**.
1. Laat de andere **Een geheime** eigenschappen maken met hun standaardwaarden.
1. Selecteer **Maken**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Een Key Vault-verwijzing toevoegen aan app-configuratie

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Alle resources**en selecteer vervolgens de instantie app-configuratiearchief die u in de snelstart hebt gemaakt.

1. Selecteer **Configuratieverkenner**.

1. Selecteer **+ Referentie van** > **de toetskluis maken**en geef vervolgens de volgende waarden op:
    - **Sleutel:** Selecteer **TestApp:Instellingen:KeyVaultMessage**.
    - **Label:** Laat deze waarde leeg.
    - **Abonnement,** **Resourcegroep**en **Sleutelkluis:** voer de waarden in die overeenkomen met die in de sleutelkluis die u in de vorige sectie hebt gemaakt.
    - **Geheim:** selecteer het geheime bericht met de naam **Message** dat u in de vorige sectie hebt gemaakt.

## <a name="connect-to-key-vault"></a>Verbinding maken met Key Vault

1. In deze zelfstudie gebruikt u een serviceprincipal voor verificatie naar Key Vault. Als u deze serviceprincipal wilt maken, gebruikt u de opdracht Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Met deze bewerking wordt een reeks sleutel-/waardeparen geretourneerd:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Voer de volgende opdracht uit om de serviceprincipal toegang te geven tot uw sleutelkluis:

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Omgevingsvariabelen toevoegen om de waarden van *clientId,* *clientSecret*en *tenantId op*te slaan .

    #### <a name="windows-command-prompt"></a>[Opdrachtprompt voor Windows](#tab/cmd)

    ```cmd
    setx AZURE_CLIENT_ID <clientId-of-your-service-principal>
    setx AZURE_CLIENT_SECRET <clientSecret-of-your-service-principal>
    setx AZURE_TENANT_ID <tenantId-of-your-service-principal>
    ```

    #### <a name="powershell"></a>[Powershell](#tab/powershell)

    ```PowerShell
    $Env:AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    $Env:AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    $Env:AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    export AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    export AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    ---

    > [!NOTE]
    > Deze Key Vault-referenties worden alleen in uw toepassing gebruikt. Uw toepassing verifieert rechtstreeks naar Key Vault met deze referenties. Ze worden nooit doorgegeven aan de app-configuratieservice.

1. Start uw terminal opnieuw op om deze nieuwe omgevingsvariabelen te laden.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Uw code bijwerken om een Key Vault-referentie te gebruiken

1. Voeg een verwijzing toe naar de vereiste NuGet-pakketten door de volgende opdracht uit te voeren:

    ```dotnetcli
    dotnet add package Microsoft.Azure.KeyVault
    dotnet add package Azure.Identity
    ```

1. Open *Program.cs*en voeg verwijzingen toe naar de volgende vereiste pakketten:

    ```csharp
    using Microsoft.Azure.KeyVault;
    using Azure.Identity;
    ```

1. Werk `CreateWebHostBuilder` de methode bij om `config.AddAzureAppConfiguration` app-configuratie te gebruiken door de methode aan te roepen. Neem `ConfigureKeyVault` de optie op en geef de juiste referenties door aan uw Key Vault.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

1. Wanneer u de verbinding met app-configuratie hebt geïnitialiseerd, stelt u de verbinding met Key Vault in door de `ConfigureKeyVault` methode aan te roepen. Na de initialisatie hebt u toegang tot de waarden van Key Vault-verwijzingen op dezelfde manier als de waarden van de normale app-configuratiesleutels.

    Als u dit proces in actie wilt zien, opent u *Index.cshtml* in de map **Weergaven** > **start.** Vervang de inhoud ervan door de volgende code:

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    U hebt toegang tot de waarde van de Key Vault-referentie **TestApp:Instellingen:KeyVaultMessage** op dezelfde manier als voor de configuratiewaarde van **TestApp:Instellingen:Bericht**.

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Als u de app wilt bouwen met de .NET Core CLI, voert u de volgende opdracht uit in de opdrachtshell:

    ```dotnetcli
    dotnet build
    ```

1. Nadat de build is voltooid, gebruikt u de volgende opdracht om de web-app lokaal uit te voeren:

    ```dotnetcli
    dotnet run
    ```

1. Open een browservenster en `http://localhost:5000`ga naar , wat de standaard-URL is voor de web-app die lokaal wordt gehost.

    ![Quickstart lokale app starten](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een app-configuratiesleutel gemaakt die verwijst naar een waarde die is opgeslagen in Key Vault. Ga door naar de volgende zelfstudie voor meer informatie over het toevoegen van een door Azure beheerde servicedie de toegang tot app-configuratie en sleutelkluis stroomlijnt.

> [!div class="nextstepaction"]
> [Beheerde identiteitsintegratie](./howto-integrate-azure-managed-service-identity.md)
