---
title: Quick Start-Azure Key Vault-client bibliotheek voor .NET (v4)
description: Meer informatie over het maken, ophalen en verwijderen van geheimen van een Azure-sleutel kluis met behulp van de .NET-client bibliotheek (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/12/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 46ffcfbe382a974b2e8cf465f35bf9eff77dbb7e
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983173"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Snelstartgids: Azure Key Vault-client bibliotheek voor .NET (SDK v4)

Ga aan de slag met de Azure Key Vault-client bibliotheek voor .NET. Volg de onderstaande stappen om het pakket te installeren en voorbeeld code voor basis taken uit te proberen.

Met Azure Key Vault kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Gebruik de Key Vault-client bibliotheek voor .NET voor het volgende:

- Verbeter de beveiliging en controle over sleutels en wacht woorden.
- Versleutelings sleutels maken en importeren in een paar minuten.
- Verminder de latentie met Cloud schaal en wereld wijde redundantie.
- Vereenvoudig en Automatiseer taken voor TLS/SSL-certificaten.
- Gebruik FIPS 140-2 level 2 Validated Hsm's.

[API reference documentation](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet) | [Bron](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)code | pakket voor de documentatie bibliotheek van de API-naslag gids[(NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* De [.net Core 3,1 SDK of hoger](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/overview)

In deze Quick Start wordt ervan uitgegaan dat u `dotnet`werkt met, [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest)en Windows-opdrachten in een Windows-Terminal (zoals [Power shell core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows Power shell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)of de [Azure Cloud shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Instellen

### <a name="create-new-net-console-app"></a>Nieuwe .NET-console-app maken

Gebruik in een console venster de opdracht `dotnet new` voor het maken van een nieuwe .net-console-app `key-vault-console-app`met de naam.

```console
dotnet new console -n key-vault-console-app
```

Wijzig uw directory in de zojuist gemaakte app-map. U kunt de toepassing samen stellen met:

```console
dotnet build
```

De build-uitvoer mag geen waarschuwingen of fouten bevatten.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Het pakket installeren

Installeer in het console venster de Azure Key Vault-client bibliotheek voor .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

Voor deze Quick start moet u ook de volgende pakketten installeren:

```console
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Een resource groep en sleutel kluis maken

In deze Snelstartgids wordt gebruikgemaakt van een vooraf gemaakte Azure-sleutel kluis. U kunt een sleutel kluis maken met behulp van de stappen in de [Snelstartgids van Azure cli](quick-create-cli.md), [Azure PowerShell Snelstartgids](quick-create-powershell.md)of [Azure Portal Quick](quick-create-portal.md)start. U kunt ook eenvoudigweg de volgende Azure CLI-opdrachten uitvoeren.

> [!Important]
> Elke sleutel kluis moet een unieke naam hebben. Vervang <uw-unieke kluis naam> door de naam van uw sleutel kluis in de volgende voor beelden.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name <your-unique-keyvault-name> -ResourceGroupName myResourceGroup -Location EastUS
```

### <a name="create-a-service-principal"></a>Een service-principal maken

De eenvoudigste manier om een op de cloud gebaseerde .NET-toepassing te verifiëren, is met een beheerde identiteit. Zie [een beheerde app Service-id gebruiken voor toegang tot Azure Key Vault](../general/managed-identity.md) voor meer informatie. 

In het omwille van de eenvoud maakt deze Snelstartgids echter een .NET-console toepassing, waarvoor het gebruik van een Service-Principal en een toegangscontrole beleid is vereist. Voor uw service-principal is een unieke naam vereist in de&lt;indeling ' http://My-Unique-service-&gt;Principle-Name '.

Maak een service-principal met behulp van de opdracht Azure CLI [AZ AD SP create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principle-name&gt;" --sdk-auth
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
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Maak een service-principal met behulp van Azure PowerShell opdracht [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) :

```azurepowershell
# Create a new service principal
$spn = New-AzADServicePrincipal -DisplayName "http://&lt;my-unique-service-principle-name&gt;"

# Get the tenant ID and subscription ID of the service principal
$tenantId = (Get-AzContext).Tenant.Id
$subscriptionId = (Get-AzContext).Subscription.Id

# Get the client ID
$clientId = $spn.ApplicationId

# Get the client Secret
$bstr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($spn.Secret)
$clientSecret = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
```

Voor meer informatie over de service-principal met Azure PowerShell raadpleegt u [een Azure-service-principal maken met Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Let op de clientId, clientSecret en tenantId, aangezien we deze in de volgende stappen gaan gebruiken.


#### <a name="give-the-service-principal-access-to-your-key-vault"></a>De Service-Principal toegang verlenen tot uw sleutel kluis

Maak een toegangs beleid voor de sleutel kluis die machtigingen verleent aan uw Service-Principal door de clientId door te geven aan de opdracht [AZ-set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Geef de Service-Principal Get, List en stel machtigingen voor zowel sleutels als geheimen.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions list get set delete purge
```

```azurepowershell
Set-AzKeyVaultAccessPolicy -VaultName <your-unique-keyvault-name> -ServicePrincipalName <clientId-of-your-service-principal> -PermissionsToSecrets list,get,set,delete,purge
```

#### <a name="set-environmental-variables"></a>Omgevings variabelen instellen

De methode DefaultAzureCredential in onze toepassing is afhankelijk van drie omgevings variabelen `AZURE_CLIENT_ID`: `AZURE_CLIENT_SECRET`, en `AZURE_TENANT_ID`. Gebruik deze variabelen instellen op de clientId-, clientSecret-en tenantId-waarden die u hebt genoteerd in de stap [een service-principal maken](#create-a-service-principal) hierboven.

U moet de naam van de sleutel kluis ook opslaan als een omgevings variabele `KEY_VAULT_NAME`met de naam.

```console
setx AZURE_CLIENT_ID <your-clientID>

setx AZURE_CLIENT_SECRET <your-clientSecret>

setx AZURE_TENANT_ID <your-tenantId>

setx KEY_VAULT_NAME <your-key-vault-name>
````

Telkens wanneer u belt `setx`, ontvangt u een antwoord op ' geslaagd: de opgegeven waarde is opgeslagen. '

```shell
AZURE_CLIENT_ID=<your-clientID>

AZURE_CLIENT_SECRET=<your-clientSecret>

AZURE_TENANT_ID=<your-tenantId>

KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Object model

Met de Azure Key Vault-client bibliotheek voor .NET kunt u sleutels en gerelateerde assets beheren, zoals certificaten en geheimen. In de onderstaande code voorbeelden ziet u hoe u een-client maakt, een geheim instelt, een geheim ophaalt en een geheim verwijdert.

De volledige console-app is beschikbaar https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-appop.

## <a name="code-examples"></a>Codevoorbeelden

### <a name="add-directives"></a>Instructies toevoegen

Voeg de volgende instructies toe aan het begin van de code:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

Het verifiëren van uw sleutel kluis en het maken van een sleutel kluis-client is afhankelijk van de omgevings variabelen in de bovenstaande stap [omgevings variabelen instellen](#set-environmental-variables) . De naam van de sleutel kluis wordt uitgebreid naar de sleutel kluis-URI in de indeling ' https://\<your-sleutel-kluis-name\>. Vault.Azure.net '.

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Een geheim opslaan

Nu uw toepassing is geverifieerd, kunt u een geheim in uw sleutel kluis plaatsen met behulp van de [client. SetSecret-methode](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) hiervoor is een naam vereist voor het geheim. in dit voor beeld gebruiken we ' mySecret '.  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

U kunt controleren of het geheim is ingesteld met de opdracht [AZ sleutel kluis Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>Een geheim ophalen

U kunt nu de eerder ingestelde waarde met de [client ophalen. Methode GetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

Uw geheim wordt nu opgeslagen als `secret.Value`.

### <a name="delete-a-secret"></a>Een geheim verwijderen

Ten slotte gaan we het geheim verwijderen uit de sleutel kluis met de- [client. Methode DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

U kunt controleren of het geheim is verdwenen met de opdracht [AZ sleutel kluis Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de Azure CLI of Azure PowerShell gebruiken om uw sleutel kluis en de bijbehorende resource groep te verwijderen.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Voorbeeldcode

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
            string secretName = "mySecret";

            string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

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
}
```


## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u een sleutel kluis gemaakt, een geheim opgeslagen en dat geheim opgehaald. Bekijk de [volledige console-app in github](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app).

Ga verder met de volgende artikelen voor meer informatie over Key Vault en hoe u deze integreert met uw toepassingen.

- [Service-naar-service-verificatie implementeren voor het Azure Key Vault met behulp van .net](../general/service-to-service-authentication.md)
- Een [overzicht van Azure Key Vault](../general/overview.md) lezen
- Raadpleeg de [Azure Key Vault hand leiding voor ontwikkel aars](../general/developers-guide.md)
- [Azure Key Vault aanbevolen procedures](../general/best-practices.md) controleren
