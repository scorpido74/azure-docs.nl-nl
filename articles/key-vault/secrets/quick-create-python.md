---
title: 'Quickstart: Azure Key Vault Python-clientbibliotheek - geheimen beheren'
description: Meer informatie over het maken, ophalen en verwijderen van geheimen van een Azure-sleutelkluis met behulp van de clientbibliotheek voor Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: cd8a5751c018b9b3b3b2ef96765545f2edab685b
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489201"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>Quickstart: Azure Key Vault-clientbibliotheek met geheimen voor Python

Aan de slag met de Azure Key Vault-clientbibliotheek voor Python. Volg de onderstaande stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen. Door Key Vault te gebruiken om geheimen op te slaan, voorkomt u dat geheimen in uw code worden opgeslagen en is uw app dus beter beveiligd.

[API-referentiedocumentatie](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python) | [Broncode van de bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets) | [Pakket (Python Package Index)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="set-up-your-local-environment"></a>Uw lokale omgeving instellen

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Installeer de Key Vault-geheimenbibliotheek:

    ```terminal
    pip install azure-keyvault-secrets
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Een resourcegroep en sleutelkluis maken

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>De service-principal toegang verlenen tot uw sleutelkluis

Voer de volgende [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)-opdracht uit om uw service-principal te autoriseren voor de bewerkingen Ophalen, Vermelden en Instellen op geheimen. Deze opdracht is afhankelijk van de omgevingsvariabelen `KEY_VAULT_NAME` en `AZURE_CLIENT_ID` die in vorige stappen zijn gemaakt.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

---

Deze opdracht is afhankelijk van de omgevingsvariabelen `KEY_VAULT_NAME` en `AZURE_CLIENT_ID` die in vorige stappen zijn gemaakt.

Zie [Een toegangsbeleid toewijzen - CLI](../general/assign-access-policy-cli.md) voor meer informatie

## <a name="create-the-sample-code"></a>De voorbeeldcode maken

Met de Azure Key Vault-clientbibliotheek voor Python kunt u geheimen en gerelateerde assets, zoals certificaten en cryptografische sleutels, beheren. Het volgende codevoorbeeld laat zien hoe u een client maakt, een geheim instelt, een geheim ophaalt en een geheim verwijdert.

Maak een bestand met de naam *kv_secrets.py* dat deze code bevat.

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = input("Input a name for your secret > ")
secretValue = input("Input a value for your secret > ")

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...")

client.set_secret(secretName, secretValue)

print(" done.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>De code uitvoeren

Zorg ervoor dat de code in de vorige sectie in een bestand met de naam *kv_secrets.py* staat. Voer de code vervolgens uit met de volgende opdracht:

```terminal
python kv_secrets.py
```

- Als er machtigingsfouten optreden, controleer dan of u de [`az keyvault set-policy`-opdracht](#give-the-service-principal-access-to-your-key-vault) hebt uitgevoerd.
- Als u de code opnieuw uitvoert met dezelfde geheime naam, treedt mogelijk deze fout op: "(Conflict) Geheim <name> heeft momenteel de status Verwijderd maar herstelbaar." Gebruik een andere geheime naam.

## <a name="code-details"></a>Codedetails

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

In de voorgaande code gebruikt het object [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) de omgevingsvariabelen die u voor uw service-principal hebt gemaakt. U geeft deze referentie op telkens wanneer u een clientobject van een Azure-bibliotheek maakt, zoals [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python), samen met de URI van de resource waarmee u wilt werken via die client:

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Een geheim opslaan

Zodra u het clientobject voor de sleutelkluis hebt opgehaald, kunt u een geheim opslaan met behulp van de methode [set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#set-secret-name--value----kwargs-): 

```python
client.set_secret(secretName, secretValue)
```

Wanneer een `set_secret` wordt aangeroepen, wordt er een aanroep bij de Azure REST-API voor de sleutelkluis gegenereerd.

Bij het verwerken van de aanvraag verifieert Azure de identiteit van de aanroepende functie (de service-principal) met behulp van het referentieobject dat u aan de client hebt verstrekt.

Het controleert ook of de aanroepende functie is geautoriseerd om de aangevraagde actie uit te voeren. U verleende deze autorisatie eerder aan de service-principal met behulp van de [`az keyvault set-policy`-opdracht](#give-the-service-principal-access-to-your-key-vault).

### <a name="retrieve-a-secret"></a>Een geheim ophalen

Om een geheim uit Key Vault te lezen, gebruikt u de methode [get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-):

```python
retrieved_secret = client.get_secret(secretName)
 ```

De geheime waarde bevindt zich in `retrieved_secret.value`.

U kunt ook een geheim ophalen met de Azure CLI-opdracht [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show).

### <a name="delete-a-secret"></a>Een geheim verwijderen

Als u een geheim wilt verwijderen, gebruikt u de methode [begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#begin-delete-secret-name----kwargs-) :

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

De methode `begin_delete_secret` is asynchroon en retourneert een pollerobject. Wanneer de methode `result` van de poller wordt aangeroepen, wordt er gewacht totdat deze voltooid is.

U kunt controleren of het geheim is verwijderd met de Azure CLI-opdracht [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show).

Zodra een geheim is verwijderd, heeft deze nog een tijdje de status Verwijderd maar herstelbaar. Gebruik een andere geheime naam als u de code nogmaals uitvoert.

## <a name="clean-up-resources"></a>Resources opschonen

Als u ook met [certificaten](../certificates/quick-create-python.md) en [sleutels](../keys/quick-create-python.md) wilt experimenteren, kunt u de Key Vault hergebruiken die in dit artikel is gemaakt.

Anders, wanneer u klaar bent met de resources die in dit artikel zijn gemaakt, gebruikt u de volgende opdracht om de resourcegroep en alle resources daarin te verwijderen:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Key Vault](../general/overview.md)
- [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- [Best practices voor Azure Key Vault](../general/best-practices.md)
- [Verifiëren met Key Vault](../general/authentication.md)
