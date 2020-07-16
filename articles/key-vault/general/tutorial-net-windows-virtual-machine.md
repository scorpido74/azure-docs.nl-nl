---
title: 'Zelfstudie: Azure Key Vault gebruiken met een virtuele Windows-machine in .NET | Microsoft Docs'
description: In deze zelfstudie gaat u een ASP.NET Core-toepassing configureren voor het lezen van een geheim in uw sleutelkluis.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 8db1c511ab9defb140720655588b27279a0f08be
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085477"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Zelfstudie: Azure Key Vault gebruiken met een Windows-VM in .NET

Azure Key Vault helpt u bij het beveiligen van geheimen zoals API-sleutels, de databaseverbindingsreeksen die nodig zijn voor toegang tot uw toepassingen, services en IT-resources.

In deze zelfstudie leert u hoe u ervoor zorgt dat een consoletoepassing informatie ophaalt uit Azure Key Vault. De toepassing gebruikt de beheerde identiteit van de virtuele machine voor het verifiëren van Key Vault. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een resourcegroep.
> * Een sleutelkluis maken.
> * Voeg een geheim toe aan de sleutelkluis.
> * Een geheim ophalen uit de sleutelkluis.
> * Een virtuele Azure-machine maken.
> * Een [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) inschakelen voor de virtuele machine.
> * Wijs machtigingen toe aan de VM-identiteit.

Lees voordat u verdergaat eerst [Basisconcepten van Key Vault](basic-concepts.md). 

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.

## <a name="prerequisites"></a>Vereisten

Voor Windows, Mac en Linux:
  * [Git](https://git-scm.com/downloads)
  * De [.NET Core 3.1 SDK of hoger](https://dotnet.microsoft.com/download/dotnet-core/3.1).
  * [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-resources-and-assign-permissions"></a>Resources maken en machtigingen toewijzen

Voordat u begint met het coderen, moet u enkele resources maken, een geheim in uw sleutelkluis plaatsen en machtigingen toewijzen.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u zich met behulp van Azure CLI wilt aanmelden bij Azure, voert u het volgende in:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een resourcegroep met de opdracht [az group create](/cli/azure/group#az-group-create). 

In dit voorbeeld wordt een resourcegroep gemaakt op de locatie VS - west:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Uw zojuist gemaakte resourcegroep wordt in de hele zelfstudie gebruikt.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Een sleutelkluis maken en deze vullen met een geheim

Maak een sleutelkluis in de resourcegroep met behulp van de opdracht [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) met de volgende gegevens:

* Naam van de sleutelkluis: een tekenreeks van 3 tot en met 24 tekens, die alleen cijfers (0-9), letters (a-z, A-Z) en afbreekstreepjes (-) mag bevatten
* Naam van de resourcegroep
* Locatie: **VS - west**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Uw Azure-account is nu als enige gemachtigd om bewerkingen op deze nieuwe sleutelkluis uit te voeren.

Voeg nu een geheim toe aan uw sleutelkluis met de opdracht [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)


Typ de volgende opdracht om een geheim te maken in de sleutelkluis met de naam **AppGeheim**:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Met dit geheim wordt de waarde **MijnGeheim** opgeslagen.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken
Gebruik een van de volgende manieren om een virtuele machine te maken:

* [De Azure CLI](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>Een identiteit toewijzen aan de virtuele machine
Maak een door het systeem toegewezen identiteit voor de virtuele machine met de opdracht [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign):

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Bekijk de door het systeem toegewezen identiteit die wordt weergegeven in de volgende code. De uitvoer van de vorige opdracht ziet er als volgt uit: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>Machtigingen toewijzen aan de VM-identiteit
Wijs de eerder gemaakte identiteitsmachtigingen toe aan uw sleutelkluis met de opdracht [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy):

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Aanmelden bij de virtuele machine

Als u zich wilt aanmelden bij de virtuele machine, volgt u de instructies in [Verbinding maken met en aanmelden bij een virtuele Azure-machine met Windows](../../virtual-machines/windows/connect-logon.md).

## <a name="set-up-the-console-app"></a>De console-app instellen

Maak een console-app en installeer de vereiste pakketten met de opdracht `dotnet`.

### <a name="install-net-core"></a>.NET Core installeren

Als u .NET Core wilt installeren, gaat u naar de pagina [.NET-downloads](https://www.microsoft.com/net/download).

### <a name="create-and-run-a-sample-net-app"></a>Een .NET-voorbeeld-app maken en uitvoeren

Open een opdrachtprompt.

U kunt 'Hallo wereld' afdrukken naar de console door de volgende opdrachten uit te voeren:

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>Het pakket installeren

Installeer vanuit het consolevenster de Azure Key Vault Secrets-clientbibliotheek voor .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

Voor deze quickstart moet u het volgende identiteitspakket installeren om u te verifiëren bij Azure Key Vault:

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>De console-app bewerken

Open het bestand *Program.cs* en voeg deze pakketten toe:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Voeg deze regels toe, waarbij u de URI bijwerkt volgens de `vaultUri` van uw sleutelkluis. De onderstaande code maakt gebruik van ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) voor verificatie naar de sleutelkluis, die een token van de beheerde identiteit van de toepassing gebruikt voor verificatie. Er wordt ook exponentieel uitstel gebruikt voor nieuwe pogingen wanneer de sleutelkluis wordt vertraagd.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            var kvUri = "https://<your-key-vault-name>.vault.azure.net";
            SecretClientOptions options = new SecretClientOptions()
            {
                Retry =
                {
                    Delay= TimeSpan.FromSeconds(2),
                    MaxDelay = TimeSpan.FromSeconds(16),
                    MaxRetries = 5,
                    Mode = RetryMode.Exponential
                 }
            };

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential(),options);

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
```

In de voorgaande code ziet u hoe u bewerkingen met Azure Key Vault uitvoert op een virtuele Windows-machine.

## <a name="clean-up-resources"></a>Resources opschonen

Als ze niet meer nodig zijn, verwijdert u de virtuele machine en de sleutelkluis.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [REST-API van Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
