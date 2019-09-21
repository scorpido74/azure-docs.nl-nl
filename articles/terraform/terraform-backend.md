---
title: Azure Storage gebruiken als een terraform-back-end
description: Een inleiding tot het opslaan van de terraform-status in Azure Storage.
services: terraform
author: tomarchermsft
ms.service: azure
ms.topic: article
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: e9b447f4f4dc9d0ee090da9729e483cc17ac7c15
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169942"
---
# <a name="store-terraform-state-in-azure-storage"></a>Store terraform-status in Azure Storage

De terraform-status wordt gebruikt voor het afstemmen van geïmplementeerde resources met terraform-configuraties. Terraform weet wat Azure-resources kunnen toevoegen, bijwerken of verwijderen. De terraform-status wordt standaard lokaal opgeslagen wanneer *terraform*wordt uitgevoerd. Deze configuratie is om een aantal redenen niet ideaal:

- De lokale status werkt niet goed in een team of een samenwerkings omgeving
- De status terraform kan gevoelige informatie bevatten
- Als u de status lokaal opslaat, neemt de kans op onbedoeld verwijderen toe

Terraform bevat het concept van een status back-end, een externe opslag voor de status terraform. Wanneer u een status back-end gebruikt, wordt het status bestand opgeslagen in een gegevens archief, zoals Azure Storage. Dit document bevat informatie over het configureren en gebruiken van Azure Storage als een terraform-status back-end.

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

De back-end van de terraform-status is geconfigureerd bij het uitvoeren van *terraform init*. Voor het configureren van de status back-end zijn de volgende gegevens vereist.

- storage_account_name: de naam van het Azure Storage-account.
- container_name: de naam van de BLOB-container.
- sleutel-de naam van het status archief bestand dat moet worden gemaakt.
- access_key: de toegangs sleutel voor opslag.

Elk van deze waarden kan worden opgegeven in het configuratie bestand van de terraform of op de opdracht regel, maar het is raadzaam om een omgevings variabele `access_key`te gebruiken voor de. Als u een omgevings variabele gebruikt, voor komt u dat de sleutel naar de schijf wordt geschreven.

Maak een omgevings variabele `ARM_ACCESS_KEY` met de naam met de waarde van de toegangs sleutel Azure Storage.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Als u de toegangs sleutel voor het Azure Storage account verder wilt beveiligen, slaat u deze op in Azure Key Vault. De omgevings variabele kan vervolgens worden ingesteld met behulp van een opdracht zoals de volgende. Raadpleeg de [Azure Key Vault documentatie][azure-key-vault]voor meer informatie over Azure Key Vault.

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Als u terraform wilt configureren voor het gebruik van de back-end, neemt u een *back-end* -configuratie op met een type *Azurerm* in de terraform-configuratie. Voeg de waarden voor *storage_account_name*, *container_name*en *Key* toe aan het configuratie blok.

In het volgende voor beeld wordt een terraform-back-end geconfigureerd en wordt een Azure-resource groep gemaakt. Vervang de waarden door waarden uit uw omgeving.

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

Initialiseer nu de configuratie met *terraform init* en voer vervolgens de configuratie uit met *terraform van toepassing*. Als u klaar bent, kunt u het status bestand vinden in de Azure Storage Blob.

## <a name="state-locking"></a>Status vergren delen

Wanneer u een Azure Storage Blob gebruikt voor status opslag, wordt de blob automatisch vergrendeld vóór elke bewerking die de status schrijft. Deze configuratie voor komt meerdere gelijktijdige status bewerkingen, wat kan leiden tot beschadiging. Zie [status locking][terraform-state-lock] (Engelstalig) in de documentatie van terraform voor meer informatie.

De vergren deling kan worden weer gegeven bij het controleren van de BLOB via de Azure Portal of andere Azure-beheer Programma's.

![Azure-Blob met vergren deling](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Versleuteling 'at rest'

Standaard worden gegevens die zijn opgeslagen in een Azure-Blob versleuteld voordat ze worden bewaard in de opslag infrastructuur. Wanneer terraform de status vereist heeft, wordt deze opgehaald uit de back-end en opgeslagen in het geheugen van uw ontwikkel systeem. In deze configuratie wordt de status beveiligd in Azure Storage en niet naar uw lokale schijf geschreven.

Zie [Azure Storage-service versleuteling voor Data-at-rest][azure-storage-encryption]voor meer informatie over Azure Storage versleuteling.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over terraform-back-end-configuratie in de [terraform-back-end-documentatie][terraform-backend].

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
