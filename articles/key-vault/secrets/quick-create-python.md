---
title: 'Quickstart: Azure Key Vault Python-clientbibliotheek - geheimen beheren'
description: Meer informatie over het maken, ophalen en verwijderen van geheimen van een Azure-sleutelkluis met behulp van de clientbibliotheek voor Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: f1f044eb3af35019eaf010e118bc4a5814269e9e
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378544"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>Quickstart: Azure Key Vault-clientbibliotheek met geheimen voor Python

Aan de slag met de Azure Key Vault-clientbibliotheek voor Python. Volg de onderstaande stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen.

Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Gebruik de Key Vault-clientbibliotheek voor Python voor het volgende:

- De beveiliging en controle over sleutels en wachtwoorden verbeteren.
- In een paar minuten versleutelingssleutels maken en importeren.
- Latentie verminderen met schalen in de cloud en globale redundantie.
- Taken voor TLS/SSL-certificaten vereenvoudigen en automatiseren.
- FIPS 140-2 level 2-gevalideerde HSM's gebruiken.

[API-referentiedocumentatie](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python) | [Broncode van de bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets) | [Pakket (Python Package Index)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement (u kunt [een gratis abonnement maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
- Python 2.7, 3.5.3 of hoger
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/)

In deze quickstart wordt ervan uitgegaan dat u [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) uitvoert in een Linux-terminalvenster.

## <a name="setting-up"></a>Instellen

### <a name="install-the-package"></a>Het pakket installeren

Installeer vanuit het consolevenster de Azure Key Vault-geheimenbibliotheek voor Python.

```console
pip install azure-keyvault-secrets
```

Voor deze quickstart moet u ook het azure.identity-pakket installeren:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Een resourcegroep en sleutelkluis maken

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Een service-principal maken

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>De service-principal toegang verlenen tot uw sleutelkluis

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>Omgevingsvariabelen instellen

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>Objectmodel

Met de Azure Key Vault-clientbibliotheek voor Python kunt u sleutels en gerelateerde assets, zoals certificaten en geheimen, beheren. In de onderstaande codevoorbeelden ziet u hoe u een client maakt, een geheim instelt, een geheim ophaalt en een geheim verwijdert.

De voorbeeld-app waarin vergelijkbare bewerkingen worden getoond als in dit artikel, evenals andere Key Vault functies, is beschikbaar op [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets/samples).

## <a name="code-examples"></a>Codevoorbeelden

### <a name="add-directives"></a>Instructies toevoegen

Voeg de volgende instructies toe aan het begin van de code:

```python
import os
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

Het verifiëren van uw sleutelkluis en het maken van een sleutelkluis-client is afhankelijk van de omgevingsvariabelen in de stap [Omgevingsvariabelen instellen](#set-environmental-variables) hierboven. De naam van de sleutelkluis wordt uitgebreid naar de sleutelkluis-URI, met de indeling https://<uw-key-vault-naam>.vault.azure.net.

```python
credential = DefaultAzureCredential()

client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Een geheim opslaan

Nu uw toepassing is geverifieerd, kunt u een geheim in uw sleutelkluis plaatsen met behulp van de [methode set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#set-secret-name--value----kwargs-). Hiervoor is een naam vereist voor het geheim. In dit voorbeeld gebruiken we 'mySecret'.  

```python
client.set_secret(secretName, secretValue)
```

U kunt controleren of het geheim is ingesteld met de opdracht [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Een geheim ophalen

U kunt nu de eerder ingestelde waarde ophalen met de [methode get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-).

```python
retrieved_secret = client.get_secret(secretName)
 ```

Uw geheim wordt nu opgeslagen als `retrieved_secret.value`.

### <a name="delete-a-secret"></a>Een geheim verwijderen

Ten slotte gaan we het geheim uit uw sleutelkluis verwijderen met de [methode begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#begin-delete-secret-name----kwargs-).

```python
client.begin_delete_secret(secretName)
```

U kunt controleren of het geheim is verdwenen met de opdracht [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de sleutelkluis en de bijbehorende resourcegroep niet meer nodig hebt, kunt u Azure CLI of Azure PowerShell gebruiken om ze te verwijderen.

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
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = "mySecret"

print("Input the value of your secret > ")
secretValue = raw_input()

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}` ...")

client.set_secret(secretName, secretValue)

print(" done.")

print("Forgetting your secret.")
secretValue = ""
print(f"Your secret is {secretValue}.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

client.begin_delete_secret(secretName)

print(" done.")
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt, een geheim opgeslagen en dat geheim opgehaald. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- [Overzicht van Azure Key Vault](../general/overview.md)
- [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- [Best practices voor Azure Key Vault](../general/best-practices.md)
