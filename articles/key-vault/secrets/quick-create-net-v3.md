---
title: Snelstart - Azure Key Vault-clientbibliotheek voor .NET (SDK v3)
description: Meer informatie over het maken, ophalen en verwijderen van geheimen uit een Azure-sleutelkluis met behulp van de .NET-clientbibliotheek (v3)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: bdc686284711fb39e6cedb18aea4d1f5c9d1d318
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425005"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Snelstart: Azure Key Vault-clientbibliotheek voor .NET (SDK v3)

Ga aan de slag met de Azure Key Vault-clientbibliotheek voor .NET. Volg de onderstaande stappen om het pakket te installeren en probeer voorbeeldcode uit voor basistaken.

> [!NOTE]
> Deze quickstart maakt gebruik van de v3.0.4-versie van de Microsoft.Azure.KeyVault-clientbibliotheek. Zie [Azure Key Vault-clientbibliotheek voor .NET (SDK v4)](quick-create-net.md)als u de meest up-to-date versie van de Key Vault-clientbibliotheek wilt gebruiken. 

Met Azure Key Vault kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Gebruik de Key Vault-clientbibliotheek voor .NET om:

- Verhoog de beveiliging en controle over sleutels en wachtwoorden.
- Maak en importeer binnen enkele minuten versleutelingssleutels.
- Verminder de latentie met cloudschaal en wereldwijde redundantie.
- Taken voor TLS/SSL-certificaten vereenvoudigen en automatiseren.
- Gebruik FIPS 140-2 Level 2 gevalideerde HSM's.

[API-naslagdocumentatie](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Elke sleutelkluis moet een unieke naam hebben. Vervang <uw-unieke-keyvault-naam> met de naam van uw sleutelkluis in de volgende voorbeelden.


## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* De [.NET Core 2.1 SDK of hoger](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/overview)

Deze quickstart gaat ervan `dotnet`uit dat u opdrachten in [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)en Windows uitvoert in een Windows-terminal (zoals [PowerShell Core,](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6) [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)of de [Azure Cloud Shell).](https://shell.azure.com/)

## <a name="setting-up"></a>Instellen

### <a name="create-new-net-console-app"></a>Nieuwe .NET-console-app maken

Gebruik de `dotnet new` opdracht om in een consolevenster een nieuwe `akv-dotnet`.NET-console-app met de naam te maken.


```console
dotnet new console -n akvdotnet
```

Wijzig uw map in de nieuw gemaakte app-map. U de toepassing bouwen met:

```console
dotnet build
```

De buildoutput mag geen waarschuwingen of fouten bevatten.

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

Deze quickstart maakt gebruik van een vooraf gemaakte Azure-sleutelkluis. U een sleutelkluis maken door de stappen te volgen in de quickstart van [Azure CLI,](quick-create-cli.md) [Azure PowerShell snelstart](quick-create-powershell.md)of [Azure-portal snelstart](quick-create-portal.md). U ook de azure cli-opdrachten hieronder eenvoudig uitvoeren.

> [!Important]
> Elke sleutelkluis moet een unieke naam hebben. Vervang <uw-unieke-keyvault-naam> met de naam van uw sleutelkluis in de volgende voorbeelden.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Een service-principal maken

De eenvoudigste manier om een cloudgebaseerde .NET-toepassing te verifiëren, is met een beheerde identiteit; zie [Een door Een App-service beheerde identiteit gebruiken om toegang te krijgen tot Azure Key Vault](../general/managed-identity.md) voor meer informatie. Omwille van de eenvoud echter, deze quickstart creëert een .NET console applicatie. Voor het verifiëren van een bureaubladtoepassing met Azure is het gebruik van een serviceprincipal en een toegangscontrolebeleid vereist.

Maak een serviceprincipe met de opdracht Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Deze bewerking retourneert een reeks sleutel/waardeparen. 

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

Let op de clientId en clientSecret, zoals we ze zullen gebruiken in de [Authenticeren om uw sleutel kluis](#authenticate-to-your-key-vault) stap hieronder.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Geef de serviceprincipal toegang tot uw sleutelkluis

Maak een toegangsbeleid voor uw sleutelkluis dat toestemming verleent aan uw serviceprincipal door de clientId door te geven aan de opdracht voor het beleid van de [AZ Keyvault-set.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Geef de serviceprincipal krijgen, lijst, en machtigingen instellen voor zowel sleutels als geheimen.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Objectmodel

Met de Azure Key Vault-clientbibliotheek voor .NET u sleutels en gerelateerde elementen zoals certificaten en geheimen beheren. De code voorbeelden hieronder zal u laten zien hoe je een geheim in te stellen en een geheim op te halen.

De volledige console-app https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnetis beschikbaar op .

## <a name="code-examples"></a>Codevoorbeelden

### <a name="add-directives"></a>Richtlijnen toevoegen

Voeg de volgende richtlijnen toe aan de bovenkant van uw code:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Authenticeren naar uw sleutelkluis

Deze .NET quickstart is afhankelijk van omgevingsvariabelen om referenties op te slaan die niet in code moeten worden geplaatst. 

Voordat u uw app bouwt `setx` en uitvoert, `akvClientId` `akvClientSecret`gebruikt `akvTenantId`u `akvSubscriptionId` de opdracht om de variabelen , , en de omgevingsvariabelen in te stellen op de waarden die u hierboven hebt opgemerkt.

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

**Macos**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

Wijs deze omgevingsvariabelen toe aan tekenreeksen in uw code en verifieer uw toepassing door ze door te geven aan de [klasse KeyVaultClient:](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Een geheim opslaan

Nu uw toepassing is geverifieerd, u een geheim in uw keyvault plaatsen met behulp van de [SetSecretAsync-methode](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) Dit vereist de URL van uw sleutelkluis, die in het formulier `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`is. Het vereist ook een naam voor het geheim -- we gebruiken "mySecret". 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

U controleren of het geheim is ingesteld met de [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) opdracht:

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Een geheim ophalen

U nu de eerder ingestelde waarde ophalen met de [getsecretasync-methode](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Je geheim is `keyvaultSecret.Value;`nu opgeslagen als.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer dit niet meer nodig is, u de Azure CLI of Azure PowerShell gebruiken om uw sleutelkluis en de bijbehorende brongroep te verwijderen.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt, een geheim opgeslagen en dat geheim opgehaald. Bekijk de [hele console-app in GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Ga verder naar de onderstaande artikelen voor meer informatie over Key Vault en hoe u deze integreren met uw toepassingen.

- [Service-to-service-verificatie implementeren voor Azure Key Vault met .NET](../general/service-to-service-authentication.md)
- Lees een [overzicht van Azure Key Vault](../general/overview.md)
- Bekijk de handleiding van azure [Key Vault-ontwikkelaars](../general/developers-guide.md)
- Aanbevolen [procedures voor Azure Key Vault bekijken](../general/best-practices.md)
