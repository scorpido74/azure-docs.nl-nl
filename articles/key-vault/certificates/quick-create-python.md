---
title: 'Quickstart: Azure Key Vault Python-clientbibliotheek - certificaten beheren'
description: Meer informatie over het maken, ophalen en verwijderen van certificaten van een Azure-sleutelkluis met behulp van de clientbibliotheek voor Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b9ff7397ad29ac681e21c32608ade9c6ce557c37
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89488623"
---
# <a name="quickstart-azure-key-vault-certificates-client-library-for-python"></a>Quickstart: Azure Key Vault-clientbibliotheek met certificaten voor Python

Aan de slag met de Azure Key Vault-clientbibliotheek voor Python. Volg de onderstaande stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen. Door Key Vault te gebruiken om certificaten op te slaan, voorkomt u dat certificaten in uw code worden opgeslagen en is uw app dus beter beveiligd.

[API-referentiedocumentatie](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python) | [Broncode van de bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates) | [Pakket (Python Package Index)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="set-up-your-local-environment"></a>Uw lokale omgeving instellen

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Installeer de Key Vault-certificaatbibliotheek:

    ```terminal
    pip install azure-keyvault-certificates
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Een resourcegroep en sleutelkluis maken

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>De service-principal toegang verlenen tot uw sleutelkluis

Voer de volgende [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)-opdracht uit om uw service-principal te autoriseren voor de bewerkingen Ophalen, Vermelden en Maken op certificaten.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create 
```

---

Deze opdracht is afhankelijk van de omgevingsvariabelen `KEY_VAULT_NAME` en `AZURE_CLIENT_ID` die in vorige stappen zijn gemaakt.

Zie [Een toegangsbeleid toewijzen - CLI](../general/assign-access-policy-cli.md) voor meer informatie.

## <a name="create-the-sample-code"></a>De voorbeeldcode maken

Met de Azure Key Vault-clientbibliotheek voor Python kunt u certificaten en gerelateerde assets, zoals geheimen en cryptografische sleutels, beheren. Het volgende codevoorbeeld laat zien hoe u een client maakt, een geheim instelt, een geheim ophaalt en een geheim verwijdert.

Maak een bestand met de naam *kv_certificates.py* dat deze code bevat.

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>De code uitvoeren

Zorg ervoor dat de code in de vorige sectie in een bestand met de naam *kv_certificates.py* staat. Voer de code vervolgens uit met de volgende opdracht:

```terminal
python kv_certificates.py
```

- Als er machtigingsfouten optreden, controleer dan of u de [`az keyvault set-policy`-opdracht](#give-the-service-principal-access-to-your-key-vault) hebt uitgevoerd.
- Als u de code opnieuw uitvoert met dezelfde sleutelnaam, treedt mogelijk deze fout op: '(Conflict) Certificaat <name> heeft momenteel de status Verwijderd maar herstelbaar.' Gebruik een andere sleutelnaam.

## <a name="code-details"></a>Codedetails

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

In de voorgaande code gebruikt het object [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) de omgevingsvariabelen die u voor uw service-principal hebt gemaakt. U geeft deze referentie op telkens wanneer u een clientobject van een Azure-bibliotheek maakt, zoals [`CertificateClient`](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python), samen met de URI van de resource waarmee u wilt werken via die client:

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Een certificaat opslaan

Zodra u het clientobject voor de sleutelkluis hebt verkregen, kunt u een certificaat maken met behulp van de [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-create-certificate-certificate-name--policy----kwargs-)-methode: 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

Hier vereist het certificaat een beleid dat is verkregen met behulp van de [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?view=azure-python#get-default--)-methode.

Wanneer een `begin_create_certificate`-methode wordt aangeroepen, wordt er een asynchrone aanroep bij de Azure REST-API voor de sleutelkluis gegenereerd. De asynchrone aanroep retourneert een poller-object. Roep de `result`-methode van de poller aan als u op het resultaat van de bewerking wilt wachten.

Bij het verwerken van de aanvraag verifieert Azure de identiteit van de aanroepende functie (de service-principal) met behulp van het referentieobject dat u aan de client hebt verstrekt.

Het controleert ook of de aanroepende functie is geautoriseerd om de aangevraagde actie uit te voeren. U verleende deze autorisatie eerder aan de service-principal met behulp van de [`az keyvault set-policy`-opdracht](#give-the-service-principal-access-to-your-key-vault).

### <a name="retrieve-a-certificate"></a>Een certificaat ophalen

Gebruik de [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#get-certificate-certificate-name----kwargs-)-methode voor het lezen van een certificaat uit Key Vault:

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

U kunt ook controleren of het certificaat is ingesteld met de Azure CLI-opdracht [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show).

### <a name="delete-a-certificate"></a>Een certificaat verwijderen

Gebruik de [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-delete-certificate-certificate-name----kwargs-)-methode om een certificaat te verwijderen:

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

De methode `begin_delete_certificate` is asynchroon en retourneert een pollerobject. Wanneer de methode `result` van de poller wordt aangeroepen, wordt er gewacht totdat deze voltooid is.

U kunt controleren of het certificaat is verwijderd met de Azure CLI-opdracht [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show).

Zodra een certificaat is verwijderd, heeft dit nog een tijdje de status Verwijderd maar herstelbaar. Gebruik een andere certificaatnaam als u de code nogmaals uitvoert.

## <a name="clean-up-resources"></a>Resources opschonen

Als u ook met [geheimen](../secrets/quick-create-python.md) en [sleutels](../keys/quick-create-python.md) wilt experimenteren, kunt u de Key Vault hergebruiken die in dit artikel is gemaakt.

Anders, wanneer u klaar bent met de resources die in dit artikel zijn gemaakt, gebruikt u de volgende opdracht om de resourcegroep en alle resources daarin te verwijderen:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Key Vault](../general/overview.md)
- [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- [Best practices voor Azure Key Vault](../general/best-practices.md)
- [Verifiëren met Key Vault](../general/authentication.md)
