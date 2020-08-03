---
title: 'Quickstart: Azure Key Vault Python-clientbibliotheek - certificaten beheren'
description: Meer informatie over het maken, ophalen en verwijderen van certificaten van een Azure-sleutelkluis met behulp van de clientbibliotheek voor Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: tracking-python
ms.openlocfilehash: abbe07d96daf5848d01cd1f4943d4969fe1e59d9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87043712"
---
# <a name="quickstart-azure-key-vault-certificates-client-library-for-python"></a>Quickstart: Azure Key Vault-clientbibliotheek met certificaten voor Python

Aan de slag met de Azure Key Vault-clientbibliotheek voor Python. Volg de onderstaande stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen.

Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Gebruik de Key Vault-clientbibliotheek voor Python voor het volgende:

- De beveiliging en controle over sleutels en wachtwoorden verbeteren.
- In een paar minuten versleutelingssleutels maken en importeren.
- Latentie verminderen met schalen in de cloud en globale redundantie.
- Taken voor TLS/SSL-certificaten vereenvoudigen en automatiseren.
- FIPS 140-2 level 2-gevalideerde HSM's gebruiken.

[API-referentiedocumentatie](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python) | [Broncode van de bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [Pakket (Python Package Index)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement (u kunt [een gratis abonnement maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
- Python 2.7, 3.5.3 of hoger
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/)

In deze quickstart wordt ervan uitgegaan dat u [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) uitvoert in een Linux-terminalvenster.

## <a name="setting-up"></a>Instellen

### <a name="install-the-package"></a>Het pakket installeren

Installeer vanuit het consolevenster de Azure Key Vault-certificatenbibliotheek voor Python.

```console
pip install azure-keyvault-certificates
```

Voor deze quickstart moet u ook het azure.identity-pakket installeren:

```console
pip install azure.identity
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

In deze quickstart wordt echter een desktoptoepassing gemaakt, omdat dit eenvoudiger is. Voor deze toepassing is het gebruik van een service-principal en een toegangsbeheerbeleid vereist. Voor uw service-principal is een unieke naam vereist met de notatie http://&lt;mijn-unieke-service-principal-naam&gt;.

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

Noteer de clientId en het clientSecret. Deze worden in de onderstaande stap [Omgevingsvariabele instellen](#set-environmental-variables) gebruikt.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>De service-principal toegang verlenen tot uw sleutelkluis

Maak een toegangsbeleid voor de sleutelkluis dat machtigingen verleent aan uw service-principal door de clientId door te geven aan de opdracht [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy). Geef de service-principal-machtigingen voor ophalen, weergeven en maken voor certificaten op.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --certificate-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>Omgevingsvariabelen instellen

De methode DefaultAzureCredential in de toepassing is afhankelijk van drie omgevingsvariabelen: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` en `AZURE_TENANT_ID`. Stel deze variabelen in op de clientId-, clientSecret- en tenantId-waarden die u hebt genoteerd in de stap [Een service-principal maken](#create-a-service-principal) met de indeling `export VARNAME=VALUE`. (Met deze methode worden alleen de variabelen ingesteld voor uw huidige shell en processen gemaakt vanuit de shell. Als u deze variabelen permanent wilt toevoegen aan uw omgeving, moet u het bestand `/etc/environment ` bewerken.) 

U moet de naam van de sleutelkluis ook opslaan als een omgevingsvariabele met de naam `KEY_VAULT_NAME`.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Objectmodel

Met de Azure Key Vault-clientbibliotheek voor Python kunt u sleutels en gerelateerde assets, zoals certificaten en geheimen, beheren. In de onderstaande codevoorbeelden ziet u hoe u een client maakt en een sleutel maakt, ophaalt en verwijdert.

## <a name="code-examples"></a>Codevoorbeelden

### <a name="add-directives"></a>Instructies toevoegen

Voeg de volgende instructies toe aan het begin van de code:

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

Het verifiëren van uw sleutelkluis en het maken van een sleutelkluis-client is afhankelijk van de omgevingsvariabelen in de stap [Omgevingsvariabelen instellen](#set-environmental-variables) hierboven. De naam van de sleutelkluis wordt uitgebreid naar de sleutelkluis-URI, met de indeling https://<uw-key-vault-naam>.vault.azure.net.

```python
credential = DefaultAzureCredential()

client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Een certificaat opslaan

Nu uw toepassing is geverifieerd, kunt u een zelfondertekend certificaat toevoegen aan uw sleutelkluis 

```python
certificate_operation_poller = client.begin_create_certificate(
    certificate_name=certificateName, policy=CertificatePolicy.get_default()
)
certificate = certificate_operation_poller.result()
```

U kunt controleren of het certificaat is ingesteld met de opdracht [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show):

```azurecli
az keyvault certificate show --vault-name <your-unique-keyvault-name> --name myCertificate
```

### <a name="retrieve-a-certificate"></a>Een certificaat ophalen

U kunt nu het eerder gemaakte certificaat ophalen

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Uw certificaat is nu opgeslagen als `retrieved_certificate`.

### <a name="delete-a-certificate"></a>Een certificaat verwijderen

Verwijder tot slot het certificaat uit uw sleutelkluis

```python
client.delete_certificate(certificateName)
```

U kunt controleren of het certificaat is ingesteld met de opdracht [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show):

```azurecli
az keyvault certifcate show --vault-name <your-unique-keyvault-name> --name myCertificate
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
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = "myCertificate"

print("Creating a certificate in " + keyVaultName + " called '" + certificateName  + "` ...")

certificate_operation_poller = client.begin_create_certificate(
    certificate_name=certificateName, policy=CertificatePolicy.get_default()

certificate = certificate_operation_poller.result()

print(" done.")

print("Retrieving your certificate from " + keyVaultName + ".")

retrieved_certificate = client.get_certificate(certificateName)

print("Certificate with name '{0}' was found'.".format(retrieved_certificate.name))
print("Deleting your certificate from " + keyVaultName + " ...")

client.delete_certificate(certificateName)

print(" done.")
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt, een certificaat opgeslagen en dat certificaat opgehaald. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Zie de [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- Bekijk de [best practices voor Azure Key Vault](../general/best-practices.md)
