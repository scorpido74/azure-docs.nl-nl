---
title: Quick Start-Azure Key Vault-client bibliotheek voor .NET
description: Biedt indelings-en inhouds criteria voor het schrijven van Quick starts voor Azure SDK-client bibliotheken.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 05/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: 01d1183d82d4d3a0f9d423a2dd64876fb4e3d6a4
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147567"
---
# <a name="quickstart-azure-key-vault-client-library-for-net"></a>Quickstart: Azure Key Vault-client bibliotheek voor .NET

Ga aan de slag met de Azure Key Vault-client bibliotheek voor .NET. Volg de onderstaande stappen om het pakket te installeren en voorbeeld code voor basis taken uit te proberen.

Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Gebruik de Key Vault-client bibliotheek voor .NET voor het volgende:

- Verbeter de beveiliging en controle over sleutels en wacht woorden.
- Versleutelings sleutels maken en importeren in een paar minuten.
- Verminder de latentie met Cloud schaal en wereld wijde redundantie.
- Vereenvoudig en Automatiseer taken voor SSL/TLS-certificaten.
- Gebruik FIPS 140-2 level 2 Validated Hsm's.

[](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [Bron](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/KeyVault)codepakket | voor de documentatie bibliotheek van de API-naslag gids[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)


## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* De [.net Core 2,1 SDK of hoger](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/overview)

In deze Quick `dotnet`start wordt ervan uitgegaan dat u werkt met, [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest)en Windows-opdrachten in een Windows-Terminal (zoals [Power shell core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows Power shell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)of de [Azure Cloud shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Instellen

### <a name="create-new-net-console-app"></a>Nieuwe .NET-console-app maken

Maak een nieuwe .NET core-toepassing in uw voorkeurs editor of IDE.

Gebruik in een console venster de `dotnet new` opdracht voor het maken van een nieuwe console-app met de naam. `akv-dotnet`


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
> Elk Key Vault moet een unieke naam hebben. In het volgende voor beeld wordt een Key Vault gemaakt met de naam *myKV*, maar u moet iets anders een andere naam hebben en deze Snelstartgids gebruiken.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Een service-principal maken

De eenvoudigste manier om een op de cloud gebaseerde .NET-toepassing te verifiëren, is met een beheerde identiteit. Zie [service-to-service-verificatie voor het Azure Key Vault met behulp van .net](service-to-service-authentication.md) voor meer informatie. Deze Quick starts maken echter een .NET-console toepassing. Voor het verifiëren van een bureaublad toepassing met Azure moet u een Service-Principal gebruiken.
Maak een service-principal met behulp van de opdracht Azure CLI [AZ AD SP create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

```azurecli
az ad sp create-for-rbac -n "mySP" --sdk-auth
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

Let op de clientId, clientSecret, subscriptionId en tenantId, aangezien we deze gebruiken in de [verificatie van uw sleutel kluis](#authenticate-to-your-key-vault) hieronder.

U hebt ook de appID van de service-principal nodig. U kunt dit vinden door de [ad SP-lijst](https://docs.microsoft.com/en-us/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) van AZ `--show-mine` uit te voeren met de para meter:

```azurecli
az ad sp list --show-mine
```

Het `appID` wordt weer gegeven in de geretourneerde JSON:

```json
    "appId": "2cf5aa18-0100-445a-9438-0b93e577a3ed",
```

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>De Service-Principal toegang verlenen tot uw sleutel kluis

Maak een toegangs beleid voor de sleutel kluis die machtigingen verleent aan uw service-principal. U doet dit met de opdracht [AZ-set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . De Service-Principal krijgt de machtigingen Get, List en set voor zowel de sleutels als de geheimen.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <appid-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Object model

Met de Azure Key Vault-client bibliotheek voor .NET kunt u sleutels en gerelateerde assets beheren, zoals certificaten en geheimen. In de onderstaande code voorbeelden wordt uitgelegd hoe u een geheim instelt en een geheim ophaalt.

De volledige console-app is beschikbaar https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet op.

## <a name="code-examples"></a>Code voorbeelden

### <a name="add-directives"></a>Instructies toevoegen

Voeg de volgende instructies toe aan het begin van de code:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Verifiëren bij uw sleutel kluis

Deze .NET-Snelstartgids is afhankelijk van omgevings variabelen om referenties op te slaan die niet in code mogen worden geplaatst. 

Voordat u uw app bouwt en uitvoert, gebruikt u `setx` de opdracht om de `akvClientId`variabelen `akvClientSecret`, `akvTenantId`, en `akvSubscriptionId` omgeving in te stellen op de waarden die u hierboven hebt genoteerd.

```console
setx akvClientId <your-clientID>

setx akvClientSecret <your-clientSecret>

setx akvTenantId <your-tentantId>

setx akvSubscriptionId <your-subscriptionId>
````

Elke keer dat u `setx`belt, ontvangt u een reactie van het volgende: Opgegeven waarde is opgeslagen. "

Wijs deze omgevings variabelen toe aan teken reeksen in uw code en verifieer uw toepassing door deze door te geven aan de [klasse KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient):

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Een geheim opslaan

Nu uw toepassing is geverifieerd, kunt u een geheim in uw sleutel kluis plaatsen met behulp van de [SetSecretAsync-methode](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) hiervoor moet de URL van de Key kluis in het formulier `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`staan. Er is ook een naam voor het geheim vereist: we gebruiken ' mySecret '.  Mogelijk wilt u deze teken reeksen toewijzen aan een variabele voor resue.

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
az group delete -g "myResourceGroup" -l "EastUS" 
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [volledige console-app in github](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet)
- [Meer informatie over Key Vault](key-vault-whatis.md)
