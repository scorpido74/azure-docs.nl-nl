---
title: Snelstart - Azure Key Vault-clientbibliotheek voor Python
description: Meer informatie over het maken, ophalen en verwijderen van sleutels uit een Azure-sleutelkluis met behulp van de Python-clientbibliotheek
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: e6120d5961dc31845c1322d052d46b52f4d2be6c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424179"
---
# <a name="quickstart-azure-key-vault-client-library-for-python"></a>Snelstart: Azure Key Vault-clientbibliotheek voor Python

Ga aan de slag met de Azure Key Vault-clientbibliotheek voor Python. Volg de onderstaande stappen om het pakket te installeren en probeer voorbeeldcode uit voor basistaken.

Met Azure Key Vault kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Gebruik de Key Vault-clientbibliotheek voor Python om:

- Verhoog de beveiliging en controle over sleutels en wachtwoorden.
- Maak en importeer binnen enkele minuten versleutelingssleutels.
- Verminder de latentie met cloudschaal en wereldwijde redundantie.
- Taken voor TLS/SSL-certificaten vereenvoudigen en automatiseren.
- Gebruik FIPS 140-2 Level 2 gevalideerde HSM's.

[API-naslagdocumentatie](/python/api/overview/azure/key-vault?view=azure-python) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [(Python Package Index)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2.7, 3.5.3, of hoger
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/overview)

Deze quickstart gaat ervan uit dat u [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) in een Linux-terminalvenster uitvoert.

## <a name="setting-up"></a>Instellen

### <a name="install-the-package"></a>Het pakket installeren

Installeer vanuit het consolevenster de Azure Key Vault-sleutelsbibliotheek voor Python.

```console
pip install azure-keyvault-keys
```

Voor deze quickstart moet u ook het azure.identity-pakket installeren:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Een resourcegroep en sleutelkluis maken

Deze quickstart maakt gebruik van een vooraf gemaakte Azure-sleutelkluis. U een sleutelkluis maken door de stappen te volgen in de quickstart van [Azure CLI,](quick-create-cli.md) [Azure PowerShell snelstart](quick-create-powershell.md)of [Azure-portal snelstart](quick-create-portal.md). U ook de onderstaande Azure CLI-opdrachten uitvoeren.

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

Let op de clientId en clientSecret, zoals we ze zullen gebruiken in de [set omgevingsvariabele](#set-environmental-variables) stap hieronder.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Geef de serviceprincipal toegang tot uw sleutelkluis

Maak een toegangsbeleid voor uw sleutelkluis dat toestemming verleent aan uw serviceprincipal door de clientId door te geven aan de opdracht voor het beleid van de [AZ Keyvault-set.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Geef de serviceprincipal ophalen, lijst en maak machtigingen voor sleutels.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --key-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>Omgevingsvariabelen instellen

De DefaultAzureCredential-methode in onze toepassing is `AZURE_CLIENT_ID`gebaseerd `AZURE_CLIENT_SECRET`op `AZURE_TENANT_ID`drie omgevingsvariabelen: , en . Stel deze variabelen in op de waarden clientId, clientSecret en tenantId die `export VARNAME=VALUE` u hebt opgemerkt in de [hoofdstap Een servicemaken](#create-a-service-principal) met de indeling. (Met deze methode worden alleen de variabelen voor uw huidige shell en processen die vanuit de `/etc/environment ` shell zijn gemaakt, ingesteld; om deze variabelen permanent aan uw omgeving toe te voegen, bewerk uw bestand.) 

U moet ook uw naam van de sleutelkluis opslaan als een omgevingsvariabele genaamd `KEY_VAULT_NAME`.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Objectmodel

Met de Azure Key Vault-clientbibliotheek voor Python u sleutels en gerelateerde elementen zoals certificaten en geheimen beheren. In de onderstaande codevoorbeelden ziet u hoe u een client maakt, een sleutel maakt, een sleutel ophaalt en een sleutel verwijdert.

## <a name="code-examples"></a>Codevoorbeelden

### <a name="add-directives"></a>Richtlijnen toevoegen

Voeg de volgende richtlijnen toe aan de bovenkant van uw code:

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

Het verifiëren van uw sleutelkluis en het maken van een key vault client is afhankelijk van de omgevingsvariabelen in de stap [Omgevingvariabelen instellen](#set-environmental-variables) hierboven. De naam van uw sleutelkluis wordt uitgebreid naar de sleutelkluis URI, in de indeling "https://<uw-key-vault-name>.vault.azure.net".

```python
credential = DefaultAzureCredential()

client = KeyClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-key"></a>Een sleutel opslaan

Nu uw toepassing is geverifieerd, u een sleutel in uw keyvault plaatsen 

```python
rsa_key = client.create_rsa_key(myKey,size=2048)
```

U controleren of de sleutel is ingesteld met de opdracht [AZ Keyvault-toets:](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show)

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Een sleutel ophalen

U nu de eerder gemaakte sleutel ophalen

```python
retrieved_key = client.get_key(keyName)
print(retrieve_key.name)

 ```

Uw sleutel is `retrieved_key`nu opgeslagen als .

### <a name="delete-a-key"></a>Een sleutel verwijderen

Tot slot, laten we de sleutel uit uw sleutelkluis verwijderen

```python
client.delete_key(keyName)
```

U controleren of de sleutel is verdwenen met de opdracht [az keyvault-toetsshow:](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show)

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer dit niet meer nodig is, u de Azure CLI of Azure PowerShell gebruiken om uw sleutelkluis en de bijbehorende brongroep te verwijderen.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Voorbeeldcode

```python
import os
from azure.keyvault.key import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = "myKey"

print("Creating a key in " + keyVaultName + " called '" + keyName  + "` ...")

rsa_key = client.create_rsa_key(myKey,size=2048)

print(" done.")

print("Retrieving your key from " + keyVaultName + ".")

retrieved_key = client.get_key(keyName)

print("Key with name '{0}' was found'.".format(retrieved_key.name))
print("Deleting your key from " + keyVaultName + " ...")

client.begin_delete_key(keyName).result()

print(" done.")
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt, een sleutel opgeslagen en die sleutel opgehaald. Ga verder naar de onderstaande artikelen voor meer informatie over Key Vault en hoe u deze integreren met uw toepassingen.

- Lees een [overzicht van Azure Key Vault](../general/overview.md)
- Bekijk de handleiding van azure [Key Vault-ontwikkelaars](../general/developers-guide.md)
- Aanbevolen [procedures voor Azure Key Vault bekijken](../general/best-practices.md)
