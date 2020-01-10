---
title: 'Zelf studie: terraform-status opslaan in Azure Storage'
description: Een inleiding tot het opslaan van de terraform-status in Azure Storage.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 1cc475e5070b21a7ea96585f2183c07d258acdc5
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75708421"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Zelf studie: terraform-status opslaan in Azure Storage

De terraform-status wordt gebruikt voor het afstemmen van geïmplementeerde resources met terraform-configuraties. Status kan terraform weten wat Azure-resources kunnen toevoegen, bijwerken of verwijderen. Standaard wordt de terraform-status lokaal opgeslagen wanneer u de opdracht `terraform apply` uitvoert. Deze configuratie is niet ideaal om de volgende redenen:

- De lokale status werkt niet goed in een team of een samenwerkings omgeving.
- De status terraform kan gevoelige informatie bevatten.
- Als u de status lokaal opslaat, neemt de kans op onbedoeld verwijderen toe.

Terraform ondersteunt het persistent maken van status in externe opslag. Een dergelijke ondersteunde back-end is Azure Storage. Dit document bevat informatie over het configureren en gebruiken van Azure Storage voor dit doel einde.

## <a name="configure-storage-account"></a>Opslag account configureren

Voordat u Azure Storage als een back-end gebruikt, moet u een opslag account maken. Het opslag account kan worden gemaakt met de Azure Portal, Power shell, de Azure CLI of terraform zelf. Gebruik het volgende voor beeld om het opslag account te configureren met de Azure CLI.

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

Noteer de naam van het opslag account, de container naam en de toegangs sleutel voor opslag. Deze waarden zijn nodig wanneer u de externe status configureert.

## <a name="configure-state-back-end"></a>Back-end van de status configureren

De back-end van de terraform-status wordt geconfigureerd wanneer u de opdracht `terraform init` uitvoert. De volgende gegevens zijn vereist voor het configureren van de back-end van de status:

- **storage_account_name**: de naam van het Azure Storage-account.
- **container_name**: de naam van de BLOB-container.
- **sleutel**: de naam van het status archief bestand dat moet worden gemaakt.
- **access_key**: de toegangs sleutel voor opslag.

Elk van deze waarden kan worden opgegeven in het configuratie bestand terraform of op de opdracht regel. We raden u aan een omgevings variabele voor de `access_key` waarde te gebruiken. Als u een omgevings variabele gebruikt, voor komt u dat de sleutel naar de schijf wordt geschreven.

Maak een omgevings variabele met de naam `ARM_ACCESS_KEY` met de waarde van de toegangs sleutel Azure Storage.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Als u de toegangs sleutel voor het Azure Storage account verder wilt beveiligen, slaat u deze op in Azure Key Vault. De omgevings variabele kan vervolgens worden ingesteld met behulp van een opdracht zoals de volgende. Raadpleeg de [Azure Key Vault documentatie](../key-vault/quick-create-cli.md)voor meer informatie over Azure Key Vault.

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

De volgende stappen moeten worden uitgevoerd om terraform te configureren voor het gebruik van de back-end:
- Neem een `backend` configuratie blok op met een type `azurerm`.
- Voeg een `storage_account_name`-waarde toe aan het configuratie blok.
- Voeg een `container_name`-waarde toe aan het configuratie blok.
- Voeg een `key`-waarde toe aan het configuratie blok.

In het volgende voor beeld wordt een terraform-back-end geconfigureerd en wordt een Azure-resource groep gemaakt.

```hcl
terraform {
  backend "azurerm" {
    resource_group_name   = "tstate"
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

U kunt nu het status bestand vinden in de blob van Azure Storage.

## <a name="state-locking"></a>Status vergren delen

Azure Storage-blobs worden automatisch vergrendeld vóór elke bewerking die de status schrijft. Met dit patroon worden gelijktijdige status bewerkingen voor komen, wat kan leiden tot beschadiging. 

Zie [status vergrendeling](https://www.terraform.io/docs/state/locking.html) in de terraform-documentatie voor meer informatie.

U kunt de vergren deling zien wanneer u de BLOB bestudeert via de Azure Portal of andere Azure-beheer hulpprogramma's.

![Azure-Blob met vergren deling](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Versleuteling 'at rest'

Gegevens die zijn opgeslagen in een Azure-Blob, worden versleuteld voordat ze worden bewaard. Wanneer dit nodig is, haalt terraform de status op uit de back-end en slaat deze op in het lokale geheugen. Als u dit patroon gebruikt, wordt de status nooit naar uw lokale schijf geschreven.

Zie [Azure Storage-service versleuteling voor Data-at-rest](../storage/common/storage-service-encryption.md)voor meer informatie over Azure Storage versleuteling.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Meer informatie over het gebruik van terraform in azure](/azure/terraform)
