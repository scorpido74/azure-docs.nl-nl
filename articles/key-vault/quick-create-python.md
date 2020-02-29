---
title: Quick Start-Azure Key Vault-client bibliotheek voor python
description: Meer informatie over het maken, ophalen en verwijderen van geheimen van een Azure-sleutel kluis met behulp van de python-client bibliotheek
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 72100381faa7306db43ac4b7155b2db4b58a891b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197671"
---
# <a name="quickstart-azure-key-vault-client-library-for-python"></a>Snelstartgids: Azure Key Vault-client bibliotheek voor python

Ga aan de slag met de Azure Key Vault-client bibliotheek voor python. Volg de onderstaande stappen om het pakket te installeren en voorbeeld code voor basis taken uit te proberen.

Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Gebruik de Key Vault-client bibliotheek voor python voor het volgende:

- Verbeter de beveiliging en controle over sleutels en wacht woorden.
- Versleutelings sleutels maken en importeren in een paar minuten.
- Verminder de latentie met Cloud schaal en wereld wijde redundantie.
- Vereenvoudig en Automatiseer taken voor TLS/SSL-certificaten.
- Gebruik FIPS 140-2 level 2 Validated Hsm's.

[API-referentie documentatie](/python/api/overview/azure/key-vault?view=azure-python) | - [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [pakket (python-pakket index)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2,7, 3.5.3 of hoger
- [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/overview)

In deze Snelstartgids wordt ervan uitgegaan dat u [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest) uitvoert in een Linux-Terminal venster.

## <a name="setting-up"></a>Instellen

### <a name="install-the-package"></a>Het pakket installeren

Installeer in het console venster de Azure Key Vault geheimen-bibliotheek voor python.

```console
pip install azure-keyvault-secrets
```

Voor deze Quick start moet u ook het Azure. Identity-pakket installeren:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Een resource groep en sleutel kluis maken

In deze Snelstartgids wordt gebruikgemaakt van een vooraf gemaakte Azure-sleutel kluis. U kunt een sleutel kluis maken met behulp van de stappen in de [Snelstartgids van Azure cli](quick-create-cli.md), [Azure PowerShell Snelstartgids](quick-create-powershell.md)of [Azure Portal Quick](quick-create-portal.md)start. U kunt ook de volgende Azure CLI-opdrachten uitvoeren.

> [!Important]
> Elke sleutel kluis moet een unieke naam hebben. Vervang < uw-unieke kluis naam > door de naam van uw sleutel kluis in de volgende voor beelden.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Een service-principal maken

De eenvoudigste manier om een op de cloud gebaseerde .NET-toepassing te verifiëren, is met een beheerde identiteit. Zie [een beheerde app Service-id gebruiken voor toegang tot Azure Key Vault](managed-identity.md) voor meer informatie. Deze Quick Start maakt echter een .NET-console toepassing. Als u een bureaublad toepassing met Azure wilt verifiëren, moet u een Service-Principal en toegangs beheer beleid gebruiken.

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
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Noteer de clientId-en clientSecret, aangezien we deze gebruiken in de stap [omgevings variabele instellen](#set-environmental-variables) hieronder.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>De Service-Principal toegang verlenen tot uw sleutel kluis

Maak een toegangs beleid voor de sleutel kluis die machtigingen verleent aan uw Service-Principal door de clientId door te geven aan de opdracht [AZ-set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Geef de Service-Principal Get, List en stel machtigingen voor zowel sleutels als geheimen.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Omgevings variabelen instellen

De methode DefaultAzureCredential in onze toepassing is afhankelijk van drie omgevings variabelen: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`en `AZURE_TENANT_ID`. Stel deze variabelen in op de clientId-, clientSecret-en tenantId-waarden die u hebt genoteerd in de stap [een service-principal maken](#create-a-service-principal) met behulp van de `export VARNAME=VALUE` indeling. (Met deze methode worden alleen de variabelen ingesteld voor uw huidige shell en processen die zijn gemaakt op basis van de shell; als u deze variabelen permanent wilt toevoegen aan uw omgeving, bewerkt u het `/etc/environment ` bestand.) 

U moet de naam van de sleutel kluis ook opslaan als een omgevings variabele met de naam `KEY_VAULT_NAME`.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Object model

Met de Azure Key Vault-client bibliotheek voor python kunt u sleutels en gerelateerde assets beheren, zoals certificaten en geheimen. In de onderstaande code voorbeelden ziet u hoe u een-client maakt, een geheim instelt, een geheim ophaalt en een geheim verwijdert.

De volledige console-app is beschikbaar op https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Code voorbeelden

### <a name="add-directives"></a>Instructies toevoegen

Voeg de volgende instructies toe aan het begin van de code:

```python
import os
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

Het verifiëren van uw sleutel kluis en het maken van een sleutel kluis-client is afhankelijk van de omgevings variabelen in de bovenstaande stap [omgevings variabelen instellen](#set-environmental-variables) . De naam van de sleutel kluis wordt uitgebreid naar de sleutel kluis-URI, in de indeling https://< uw-sleutel-kluis naam >. kluis. Azure. net.

```python
credential = DefaultAzureCredential()

client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Een geheim opslaan

Nu uw toepassing is geverifieerd, kunt u een geheim in uw sleutel kluis plaatsen met behulp van de client. SetSecret-methode] (/DotNet/API/Microsoft.Azure.keyvault.keyvaultclientextensions.setsecretasync) voor dit voor beeld is een naam vereist voor het geheim: we gebruiken ' mySecret ' in deze steek proef.  

```python
client.set_secret(secretName, secretValue)
```

U kunt controleren of het geheim is ingesteld met de opdracht [AZ sleutel kluis Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Een geheim ophalen

U kunt nu de eerder ingestelde waarde met de [client ophalen. Methode GetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
retrieved_secret = client.get_secret(secretName)
 ```

Uw geheim wordt nu opgeslagen als `retrieved_secret.value`.

### <a name="delete-a-secret"></a>Een geheim verwijderen

Ten slotte gaan we het geheim verwijderen uit de sleutel kluis met de- [client. Methode DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

```python
client.delete_secret(secretName)
```

U kunt controleren of het geheim is verdwenen met de opdracht [AZ sleutel kluis Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
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

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = "mySecret"

print("Input the value of your secret > ")
secretValue = raw_input()

print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...")

client.set_secret(secretName, secretValue)

print(" done.")

print("Forgetting your secret.")
secretValue = ""
print("Your secret is '" + secretValue + "'.")

print("Retrieving your secret from " + keyVaultName + ".")

retrieved_secret = client.get_secret(secretName)

print("Your secret is '" + retrieved_secret.value + "'.")
print("Deleting your secret from " + keyVaultName + " ...")

client.delete_secret(secretName)

print(" done.")
```

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u een sleutel kluis gemaakt, een geheim opgeslagen en dat geheim opgehaald. Ga verder met de volgende artikelen voor meer informatie over Key Vault en hoe u deze integreert met uw toepassingen.

- Een [overzicht van Azure Key Vault](key-vault-overview.md) lezen
- Raadpleeg de [Azure Key Vault hand leiding voor ontwikkel aars](key-vault-developers-guide.md)
- Meer informatie over [sleutels, geheimen en certificaten](about-keys-secrets-and-certificates.md)
- [Azure Key Vault aanbevolen procedures](key-vault-best-practices.md) controleren
