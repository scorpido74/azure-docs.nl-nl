---
title: 'Quickstart: Azure Key Vault-clientbibliotheek voor .NET (v4)'
description: Meer informatie over het maken, ophalen en verwijderen van geheimen van een Azure-sleutelkluis met behulp van de clientbibliotheek voor .NET (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 97578233c6b636b5ffe35fa8ff0b138903425f79
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91631695"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Quickstart: Azure Key Vault-clientbibliotheek voor .NET (SDK v4)

Ga aan de slag met de Azure Key Vault-clientgeheimenbibliotheek voor .NET. Volg de onderstaande stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen.

[API-referentiedocumentatie](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet&preserve-view=true) | [Bibliotheek broncode](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Package (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK of hoger](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure-CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)

Deze quickstart maakt gebruik van `dotnet` en Azure CLI

## <a name="setup"></a>Instellen

### <a name="create-a-new-console-app"></a>Een nieuwe console-app maken

Deze quickstart maakt gebruik van de Azure Identity-bibliotheek met Azure CLI om de gebruiker te verifiëren bij Azure-services. Ontwikkelaars kunnen ook Visual Studio of Visual Studio Code gebruiken om hun oproepen te verifiëren: zie [De client verifiëren met de Azure Identity-clientbibliotheek](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme?view=azure-dotnet#authenticate-the-client&preserve-view=true) (Engelstalig) voor meer informatie

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

1. Voer de opdracht `login` uit.

    ```azurecli-interactive
    az login
    ```

    Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een Azure-aanmeldingspagina geladen.

    Als dat niet het geval is, opent u een browserpagina op [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en voert u de autorisatiecode in die wordt weergegeven in uw terminal.

2. Meldt u zich in de browser aan met uw accountreferenties.


### <a name="create-new-net-console-app"></a>Nieuwe .NET-console-app maken

1. Voer in een opdrachtshell de volgende opdracht uit om een project te maken met de naam `key-vault-console-app`:

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Ga naar de map van zojuist gemaakte *key-vault-console-app* en voer de volgende opdracht uit om het project te bouwen:

    ```dotnetcli
    dotnet build
    ```

    De build-uitvoer mag geen waarschuwingen of fouten bevatten.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>De pakketten installeren

Installeer vanuit de opdrachtshell de Azure Key Vault-clientbibliotheek voor .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
```

Voor deze quickstart moet u ook de Azure SDK-clientbibliotheek voor Azure Identity installeren:

```dotnetcli
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Een resourcegroep en sleutelkluis maken

[!INCLUDE[Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Toegang verlenen tot uw sleutelkluis

Een toegangsbeleid maken voor de sleutelkluis waarmee geheime machtigingen aan uw gebruikersaccount worden verleend

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>Omgevingsvariabelen instellen

In deze applicatie wordt de naam van de sleutelkluis gebruikt als een omgevingsvariabele met de naam `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS of Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Objectmodel

Met de Azure Key Vault-clientgeheimenbibliotheek voor .NET kunt u geheimen beheren. In de sectie [Codevoorbeelden](#code-examples) ziet u hoe u een client maakt, een geheim instelt, een geheim ophaalt en een geheim verwijdert.

De volledige console-app is beschikbaar op https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Codevoorbeelden

### <a name="add-directives"></a>Instructies toevoegen

Voeg de volgende instructies toe aan het begin van *Program.cs*:

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

In deze quickstart wordt de aangemelde gebruiker gebruikt voor de verificatie bij de sleutelkluis. Dit is de voorkeursmethode voor lokale ontwikkeling. Voor toepassingen die zijn geïmplementeerd in Azure, moet beheerde identiteit worden toegewezen aan App Service of aan Virtuele machine. Zie [Overzicht van beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie.

In het onderstaande voorbeeld wordt de naam van de sleutelkluis uitgebreid naar de sleutelkluis-URI, met de indeling https://\<your-key-vault-name\>.vault.azure.net. In dit voorbeeld wordt de klasse ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) gebruikt, waarmee u dezelfde code kunt gebruiken in verschillende omgevingen met verschillende opties om identiteiten te bieden. Zie het artikel over [standaardverificatie van Azure-referenties](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme?view=azure-dotnet#defaultazurecredential) (Engelstalig) voor meer informatie. 

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Een geheim opslaan

Nu de console-app is geverifieerd, voegt u een geheim toe aan de sleutelkluis. Gebruik voor deze taak de methode [client.SetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync). De eerste parameter van de methode in dit voorbeeld accepteert een naam voor het geheim &mdash;"mySecret".

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

U kunt controleren of het geheim is ingesteld met de opdracht [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show&preserve-view=true):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>Een geheim ophalen

U kunt nu de eerder ingestelde waarde ophalen met de methode [client.getSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

Uw geheim wordt nu opgeslagen als `secret.Value`.

### <a name="delete-a-secret"></a>Een geheim verwijderen

Ten slotte gaan we het geheim uit uw sleutelkluis verwijderen met de methode [client.DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

U kunt controleren of het geheim is verdwenen met de opdracht [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show&preserve-view=true):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de sleutelkluis en de bijbehorende resourcegroep niet meer nodig hebt, kunt u Azure CLI of Azure PowerShell gebruiken om ze te verwijderen.

### <a name="delete-a-key-vault"></a>Een sleutelkluis verwijderen

```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>Een sleutelkluis opschonen

```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### <a name="delete-a-resource-group"></a>Een resourcegroep verwijderen

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Voorbeeldcode

Voer de volgende stappen uit om de .NET Core-console-app aan te passen voor interactie met Key Vault:

1. Vervang de code in *Program.cs* door de volgende code:

    ```csharp
    using System;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static void Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                client.SetSecret(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                KeyVaultSecret secret = client.GetSecret(secretName);
                Console.WriteLine($"Your secret is '{secret.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = client.StartDeleteSecret(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                while (!operation.HasCompleted)
                {
                    Thread.Sleep(2000);
                
                    operation.UpdateStatus();
                }
                client.PurgeDeletedSecret(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```

1. Voer de volgende opdracht uit om de app uit te voeren.

    ```dotnetcli
    dotnet run
    ```

1. Voer een geheime waarde in wanneer u hierom wordt gevraagd. Bijvoorbeeld mySecretPassword (mijn geheime wachtwoord).

    Er verschijnt een variant van de volgende uitvoer:

    ```console
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.    
    ```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt, een geheim opgeslagen en dat geheim opgehaald. Zie de [volledige console-app in GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app).

Zie de volgende artikelen voor meer informatie over Key Vault en hoe u Key Vault integreert met uw apps:

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Zie een [zelfstudie over toegang tot Key Vault vanuit een App Service-toepassing](../general/tutorial-net-create-vault-azure-web-app.md)
- Zie een [zelfstudie over toegang tot Key Vault vanuit een virtuele machine](../general/tutorial-net-virtual-machine.md)
- Zie de [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- Bekijk de [best practices voor Azure Key Vault](../general/best-practices.md)
