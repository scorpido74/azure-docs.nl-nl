---
title: Quick Start-Azure Key Vault-client bibliotheek voor .NET (SDK v3)
description: Meer informatie over het maken, ophalen en verwijderen van geheimen van een Azure-sleutel kluis met behulp van de .NET-client bibliotheek (v3)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: bdc686284711fb39e6cedb18aea4d1f5c9d1d318
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81425005"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Snelstartgids: Azure Key Vault-client bibliotheek voor .NET (SDK v3)

Ga aan de slag met de Azure Key Vault-client bibliotheek voor .NET. Volg de onderstaande stappen om het pakket te installeren en voorbeeld code voor basis taken uit te proberen.

> [!NOTE]
> In deze Quick Start wordt de v 3.0.4-versie van de client bibliotheek voor micro soft. Azure.-kluis gebruikt. Zie [Azure Key Vault-client bibliotheek voor .net (SDK v4)](quick-create-net.md)voor het gebruik van de meest recente versie van de Key Vault-client bibliotheek. 

Met Azure Key Vault kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Gebruik de Key Vault-client bibliotheek voor .NET voor het volgende:

- Verbeter de beveiliging en controle over sleutels en wacht woorden.
- Versleutelings sleutels maken en importeren in een paar minuten.
- Verminder de latentie met Cloud schaal en wereld wijde redundantie.
- Vereenvoudig en Automatiseer taken voor TLS/SSL-certificaten.
- Gebruik FIPS 140-2 level 2 Validated Hsm's.

[API reference documentation](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [Bron](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)code | pakket voor de documentatie bibliotheek van de API-naslag gids[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Elke sleutel kluis moet een unieke naam hebben. Vervang <uw-unieke kluis naam> door de naam van uw sleutel kluis in de volgende voor beelden.


## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* De [.net Core 2,1 SDK of hoger](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/overview)

In deze Quick Start wordt ervan uitgegaan dat u `dotnet`werkt met, [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest)en Windows-opdrachten in een Windows-Terminal (zoals [Power shell core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows Power shell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)of de [Azure Cloud shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Instellen

### <a name="create-new-net-console-app"></a>Nieuwe .NET-console-app maken

Gebruik in een console venster de opdracht `dotnet new` voor het maken van een nieuwe .net-console-app `akv-dotnet`met de naam.


```console
dotnet new console -n akvdotnet
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
dotnet add package Microsoft.Azure.KeyVault
```

Voor deze Quick start moet u ook de volgende pakketten installeren:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Een resource groep en sleutel kluis maken

In deze Snelstartgids wordt gebruikgemaakt van een vooraf gemaakte Azure-sleutel kluis. U kunt een sleutel kluis maken met behulp van de stappen in de [Snelstartgids van Azure cli](quick-create-cli.md), [Azure PowerShell Snelstartgids](quick-create-powershell.md)of [Azure Portal Quick](quick-create-portal.md)start. U kunt ook eenvoudigweg de volgende Azure CLI-opdrachten uitvoeren.

> [!Important]
> Elke sleutel kluis moet een unieke naam hebben. Vervang <uw-unieke kluis naam> door de naam van uw sleutel kluis in de volgende voor beelden.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Een service-principal maken

De eenvoudigste manier om een op de cloud gebaseerde .NET-toepassing te verifiëren, is met een beheerde identiteit. Zie [een beheerde app Service-id gebruiken voor toegang tot Azure Key Vault](../general/managed-identity.md) voor meer informatie. Deze Quick Start maakt echter een .NET-console toepassing. Als u een bureaublad toepassing met Azure wilt verifiëren, moet u een Service-Principal en toegangs beheer beleid gebruiken.

Maak een service-principal met behulp van de opdracht Azure CLI [AZ AD SP create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

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
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Noteer de clientId-en clientSecret, aangezien we deze gebruiken in de verificatie van [uw sleutel kluis](#authenticate-to-your-key-vault) hieronder.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>De Service-Principal toegang verlenen tot uw sleutel kluis

Maak een toegangs beleid voor de sleutel kluis die machtigingen verleent aan uw Service-Principal door de clientId door te geven aan de opdracht [AZ-set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Geef de Service-Principal Get, List en stel machtigingen voor zowel sleutels als geheimen.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Object model

Met de Azure Key Vault-client bibliotheek voor .NET kunt u sleutels en gerelateerde assets beheren, zoals certificaten en geheimen. In de onderstaande code voorbeelden wordt uitgelegd hoe u een geheim instelt en een geheim ophaalt.

De volledige console-app is beschikbaar https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnetop.

## <a name="code-examples"></a>Codevoorbeelden

### <a name="add-directives"></a>Instructies toevoegen

Voeg de volgende instructies toe aan het begin van de code:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Verifiëren bij uw sleutel kluis

Deze .NET-Snelstartgids is afhankelijk van omgevings variabelen om referenties op te slaan die niet in code mogen worden geplaatst. 

Voordat u uw app bouwt en uitvoert, gebruikt u `setx` de opdracht om de `akvClientId`variabelen `akvClientSecret`, `akvTenantId`, en `akvSubscriptionId` omgeving in te stellen op de waarden die u hierboven hebt genoteerd.

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

**MacOS**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

Wijs deze omgevings variabelen toe aan teken reeksen in uw code en verifieer uw toepassing door deze door te geven aan de [klasse KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient):

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Een geheim opslaan

Nu uw toepassing is geverifieerd, kunt u een geheim in uw sleutel kluis plaatsen met behulp van de [SetSecretAsync-methode](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) hiervoor moet de URL van de Key kluis in het formulier `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`staan. Er is ook een naam voor het geheim vereist: we gebruiken ' mySecret '. 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

U kunt controleren of het geheim is ingesteld met de opdracht [AZ sleutel kluis Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Een geheim ophalen

U kunt nu de eerder ingestelde waarde ophalen met de [methode GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Uw geheim wordt nu opgeslagen als `keyvaultSecret.Value;`.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de Azure CLI of Azure PowerShell gebruiken om uw sleutel kluis en de bijbehorende resource groep te verwijderen.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u een sleutel kluis gemaakt, een geheim opgeslagen en dat geheim opgehaald. Bekijk de [volledige console-app in github](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Ga verder met de volgende artikelen voor meer informatie over Key Vault en hoe u deze integreert met uw toepassingen.

- [Service-naar-service-verificatie implementeren voor het Azure Key Vault met behulp van .net](../general/service-to-service-authentication.md)
- Een [overzicht van Azure Key Vault](../general/overview.md) lezen
- Raadpleeg de [Azure Key Vault hand leiding voor ontwikkel aars](../general/developers-guide.md)
- [Azure Key Vault aanbevolen procedures](../general/best-practices.md) controleren
