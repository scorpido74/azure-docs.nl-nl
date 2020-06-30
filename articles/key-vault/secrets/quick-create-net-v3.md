---
title: 'Quickstart: Azure Key Vault-clientbibliotheek voor .NET (SDK v3)'
description: Meer informatie over het maken, ophalen en verwijderen van geheimen van een Azure-sleutelkluis met behulp van de clientbibliotheek voor .NET (v3)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 0dba746379a6b5338c9e67fe312c392f37399012
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/21/2020
ms.locfileid: "85125784"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Quickstart: Azure Key Vault-clientbibliotheek voor .NET (SDK v3)

Ga aan de slag met de Azure Key Vault-clientbibliotheek voor .NET. Volg de onderstaande stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen.

> [!NOTE]
> In deze quickstart wordt versie v3.0.4 van de Microsoft.Azure.KeyVault-clientbibliotheek gebruikt. Zie [Azure Key Vault-clientbibliotheek voor .NET (SDK v4)](quick-create-net.md) als u de meest recente versie van de Key Vault-clientbibliotheek wilt gebruiken. 

Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Gebruik de Key Vault-clientbibliotheek voor .NET voor het volgende:

- De beveiliging en controle over sleutels en wachtwoorden verbeteren.
- In een paar minuten versleutelingssleutels maken en importeren.
- Latentie verminderen met schalen in de cloud en globale redundantie.
- Taken voor TLS/SSL-certificaten vereenvoudigen en automatiseren.
- FIPS 140-2 level 2-gevalideerde HSM's gebruiken.

[API-referentiedocumentatie](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [Bibliotheek broncode](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Elke sleutelkluis moet een unieke naam hebben. Vervang <your-unique-keyvault-name> door de naam van uw sleutelkluis in de volgende voorbeelden.


## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement (u kunt [een gratis abonnement maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* De [.NET Core 3.1 SDK of hoger](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/overview)

In deze quickstart wordt ervan uitgegaan dat u `dotnet`, [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) en Windows-opdrachten in een Windows-terminal (zoals [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6) of [Azure Cloud Shell](https://shell.azure.com/)) uitvoert.

## <a name="setting-up"></a>Instellen

### <a name="create-new-net-console-app"></a>Nieuwe .NET-console-app maken

Gebruik in een consolevenster de opdracht `dotnet new` om een nieuwe .NET-console-app te maken met de naam `akv-dotnet`.


```console
dotnet new console -n akvdotnet
```

Wijzig uw map in de zojuist gemaakte app-map. U kunt de toepassing maken met:

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

Installeer vanuit het consolevenster de Azure Key Vault-clientbibliotheek voor .NET:

```console
dotnet add package Microsoft.Azure.KeyVault
```

Voor deze quickstart moet u ook de volgende pakketten installeren:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Een resourcegroep en sleutelkluis maken

In deze quickstart wordt gebruikgemaakt van een vooraf gemaakte Azure-sleutelkluis. U kunt een sleutelkluis maken met behulp van de stappen in de [quickstart voor Azure CLI](quick-create-cli.md), de [quickstart voor Azure PowerShell](quick-create-powershell.md) of de [quickstart voor de Azure-portal](quick-create-portal.md). U kunt ook de volgende Azure CLI-opdrachten uitvoeren.

> [!Important]
> Elke sleutelkluis moet een unieke naam hebben. Vervang <your-unique-keyvault-name> door de naam van uw sleutelkluis in de volgende voorbeelden.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Een service-principal maken

De eenvoudigste manier om een .NET-cloudtoepassing te verifiëren, is met een beheerde identiteit. Zie [Use an App Service managed identity to access Azure Key Vault](../general/managed-identity.md) (Een door App Service beheerde identiteit gebruiken om toegang te krijgen tot Azure Key Vault) voor meer informatie. 

In deze quickstart wordt echter een .NET-consoletoepassing gemaakt, omdat dit eenvoudiger is. Voor deze toepassing is het gebruik van een service-principal en een toegangsbeheerbeleid vereist. Voor uw service-principal is een unieke naam vereist met de notatie http://&lt;mijn-unieke-service-principal-naam&gt;.

Maak een service-principal met behulp van de Azure CLI-opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac):

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principal-name&gt;" --sdk-auth
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

Noteer de clientId en het clientSecret. Deze worden in de onderstaande stap [Verifiëren bij uw sleutelkluis](#authenticate-to-your-key-vault) gebruikt.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>De service-principal toegang verlenen tot uw sleutelkluis

Maak een toegangsbeleid voor de sleutelkluis dat machtigingen verleent aan uw service-principal door de clientId door te geven aan de opdracht [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy). Geef de service-principal-machtigingen voor ophalen, weergeven en instellen voor zowel sleutels als geheimen.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Objectmodel

Met de Azure Key Vault-client bibliotheek voor .NET kunt u sleutels en gerelateerde assets, zoals certificaten en geheimen, beheren. In de onderstaande codevoorbeelden ziet u hoe u een geheim instelt en ophaalt.

De volledige console-app is beschikbaar op https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet.

## <a name="code-examples"></a>Codevoorbeelden

### <a name="add-directives"></a>Instructies toevoegen

Voeg de volgende instructies toe aan het begin van de code:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Verifiëren bij uw sleutelkluis

Deze .NET-quickstart is afhankelijk van omgevingsvariabelen om referenties op te slaan die niet in code mogen worden geplaatst. 

Gebruik voordat u uw app bouwt en uitvoert de opdracht `setx` om de omgevingsvariabelen `akvClientId`, `akvClientSecret`, `akvTenantId` en `akvSubscriptionId` in te stellen op de waarden die u hierboven hebt genoteerd.

**Windows**

```console
setx akvClientId "<your-clientID>"
setx akvClientSecret "<your-clientSecret>"
```

**Linux**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

**macOS**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

Wijs deze omgevingsvariabelen toe aan tekenreeksen in uw code en verifieer uw toepassing door deze door te geven aan de [KeyVaultClient-klasse](/dotnet/api/microsoft.azure.keyvault.keyvaultclient):

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Een geheim opslaan

Nu uw toepassing is geverifieerd, kunt u een geheim in uw sleutelkluis plaatsen met behulp van de [methode SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync). Hiervoor is de URL van uw sleutelkluis vereist; deze heeft de vorm `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`. Er is ook een naam voor het geheim vereist: we gebruiken 'mySecret'. 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

U kunt controleren of het geheim is ingesteld met de opdracht [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Een geheim ophalen

U kunt nu de eerder ingestelde waarde ophalen met de [methode GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Uw geheim wordt nu opgeslagen als `keyvaultSecret.Value;`.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de sleutelkluis en de bijbehorende resourcegroep niet meer nodig hebt, kunt u Azure CLI of Azure PowerShell gebruiken om ze te verwijderen.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt, een geheim opgeslagen en dat geheim opgehaald. Zie de [volledige console-app in GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Ga verder met de volgende artikelen als u meer wilt weten over Key Vault en hoe u het integreert met uw toepassingen.

- [Service-to-service verificatie naar Azure Key Vault implementeren met behulp van .NET](../general/service-to-service-authentication.md)
- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Zie de [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- Bekijk de [best practices voor Azure Key Vault](../general/best-practices.md)
