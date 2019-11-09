---
title: 'Zelf studie: terraform-status opslaan in Azure Storage'
description: Een inleiding tot het opslaan van de terraform-status in Azure Storage.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: cb85897e0a7d281eca4ad3f42e8ef28c9e3fdb7b
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833531"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Zelf studie: terraform-status opslaan in Azure Storage

De terraform-status wordt gebruikt voor het afstemmen van geïmplementeerde resources met terraform-configuraties. Status kan terraform weten wat Azure-resources kunnen toevoegen, bijwerken of verwijderen. Standaard wordt de terraform-status lokaal opgeslagen bij het uitvoeren van de `terraform apply` opdracht. Deze configuratie is niet ideaal om de volgende redenen:

- De lokale status werkt niet goed in een team of een samenwerkings omgeving
- De status terraform kan gevoelige informatie bevatten
- Als u de status lokaal opslaat, neemt de kans op onbedoeld verwijderen toe

Terraform ondersteunt het persistent maken van status in externe opslag. Een dergelijke ondersteunde back-end is Azure Storage. Dit document bevat informatie over het configureren en gebruiken van Azure Storage voor dit doel einde.

## <a name="configure-storage-account"></a>Opslag account configureren

Voordat u Azure Storage als back-end gebruikt, moet u een opslag account maken. Het opslag account kan worden gemaakt met de Azure Portal, Power shell, de Azure CLI of terraform zelf. Gebruik het volgende voor beeld om het opslag account te configureren met de Azure CLI.

```azurecli
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

Noteer de naam van het opslag account, de container naam en de toegangs sleutel voor opslag. Deze waarden zijn nodig bij het configureren van de externe status.

## <a name="configure-state-backend"></a>Status back-end configureren

De back-end van de terraform-status wordt geconfigureerd bij het uitvoeren van de `terraform init` opdracht. de volgende gegevens zijn vereist voor het configureren van de status back-end:

- storage_account_name: de naam van het Azure Storage-account.
- container_name-de naam van de BLOB-container.
- sleutel-de naam van het status archief bestand dat moet worden gemaakt.
- access_key-de toegangs sleutel voor opslag.

Elk van deze waarden kan worden opgegeven in het configuratie bestand van de terraform of op de opdracht regel, maar het is raadzaam om een omgevings variabele te gebruiken voor de `access_key`. Als u een omgevings variabele gebruikt, voor komt u dat de sleutel naar de schijf wordt geschreven.

Maak een omgevings variabele met de naam `ARM_ACCESS_KEY` met de waarde van de toegangs sleutel Azure Storage.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Als u de toegangs sleutel voor het Azure Storage account verder wilt beveiligen, slaat u deze op in Azure Key Vault. De omgevings variabele kan vervolgens worden ingesteld met behulp van een opdracht zoals de volgende. Raadpleeg de [Azure Key Vault documentatie](../key-vault/quick-create-cli.md)voor meer informatie over Azure Key Vault.

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Als u terraform wilt configureren voor het gebruik van de back-end, moet u de volgende stappen uitvoeren:
- Neem een `backend` configuratie blok op met een type `azurerm`.
- Voeg een `storage_account_name`-waarde toe aan het configuratie blok.
- Voeg een `container_name`-waarde toe aan het configuratie blok.
- Voeg een `key`-waarde toe aan het configuratie blok.

In het volgende voor beeld wordt een terraform-back-end geconfigureerd en wordt een Azure-resource groep gemaakt.

```hcl
terraform {
  backend "azurerm" {
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

Initialiseer de configuratie door de volgende stappen uit te voeren:

1. Voer de opdracht `terraform init` uit.
1. Voer de opdracht `terraform apply` uit.

U kunt nu het status bestand vinden in de Azure Storage Blob.

## <a name="state-locking"></a>Status vergren delen

Azure Storage-blobs worden automatisch vergrendeld vóór elke bewerking die de status schrijft. Met dit patroon worden gelijktijdige status bewerkingen voor komen, wat kan leiden tot beschadiging. 

Zie [status locking](https://www.terraform.io/docs/state/locking.html) (Engelstalig) in de documentatie van terraform voor meer informatie.

De vergren deling kan worden weer gegeven bij het controleren van de BLOB via de Azure Portal of andere Azure-beheer Programma's.

![Azure-Blob met vergren deling](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Versleuteling 'at rest'

Gegevens die zijn opgeslagen in een Azure-Blob, worden versleuteld voordat ze worden bewaard. Wanneer dit nodig is, haalt terraform de status op uit de back-end en slaat deze op in het lokale geheugen. Als u dit patroon gebruikt, wordt de status nooit naar uw lokale schijf geschreven.

Zie [Azure Storage-service versleuteling voor Data-at-rest](../storage/common/storage-service-encryption.md)voor meer informatie over Azure Storage versleuteling.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Meer informatie over het gebruik van terraform in azure](/azure/terraform)