---
title: Zelfstudie - Terraform-status opslaan in Azure Storage
description: Een inleiding tot het opslaan van de terraformstatus in Azure Storage.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 1cc475e5070b21a7ea96585f2183c07d258acdc5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75708421"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Zelfstudie: Terraform-status opslaan in Azure Storage

Terraform-status wordt gebruikt om geïmplementeerde resources te verzoenen met Terraform-configuraties. Status stelt Terraform in staat om te weten welke Azure-bronnen moeten worden toegevoegd, bijgewerkt of verwijderd. De status Terraform wordt standaard lokaal opgeslagen `terraform apply` wanneer u de opdracht uitvoert. Deze configuratie is niet ideaal om de volgende redenen:

- Lokale staat werkt niet goed in een team of collaboratieve omgeving.
- Terraform staat kan gevoelige informatie bevatten.
- Als u de status lokaal opslaat, vergroot u de kans op onbedoelde verwijdering.

Terraform ondersteunt het voortbestaan van de staat in externe opslag. Een van deze ondersteunde back-end is Azure Storage. In dit document ziet u hoe u Azure Storage voor dit doel configureert en gebruikt.

## <a name="configure-storage-account"></a>Opslagaccount configureren

Voordat u Azure Storage als back-end gebruikt, moet u een opslagaccount maken. Het opslagaccount kan worden gemaakt met de Azure-portal, PowerShell, azure CLI of Terraform zelf. Gebruik het volgende voorbeeld om het opslagaccount te configureren met de Azure CLI.

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

Let op de naam van het opslagaccount, de naam van de container en de toegangssleutel voor opslag. Deze waarden zijn nodig wanneer u de externe status configureert.

## <a name="configure-state-back-end"></a>Status back-end configureren

De terraform state back-end wordt `terraform init` geconfigureerd wanneer u de opdracht uitvoert. De volgende gegevens zijn nodig om de back-end van de status te configureren:

- **storage_account_name:** de naam van het Azure Storage-account.
- **container_name:** de naam van de blobcontainer.
- **sleutel:** de naam van het bestand van het staatarchief dat moet worden gemaakt.
- **access_key**: De toegangssleutel voor de opslag.

Elk van deze waarden kan worden opgegeven in het terraformconfiguratiebestand of op de opdrachtregel. We raden u aan een `access_key` omgevingsvariabele voor de waarde te gebruiken. Als u een omgevingsvariabele gebruikt, wordt voorkomen dat de sleutel naar de schijf wordt geschreven.

Maak een omgevingsvariabele met de naam `ARM_ACCESS_KEY` van de waarde van de toegangssleutel voor Azure Storage.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Als u de toegangssleutel voor Azure Storage-accounts verder wilt beschermen, slaat u deze op in Azure Key Vault. De omgevingsvariabele kan vervolgens worden ingesteld met behulp van een opdracht die vergelijkbaar is met de volgende opdracht. Zie de [Azure Key Vault-documentatie](../key-vault/quick-create-cli.md)voor meer informatie over Azure Key Vault.

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Om Terraform te configureren om de back-end te gebruiken, moeten de volgende stappen worden uitgevoerd:
- Neem `backend` een configuratieblok op `azurerm`met een type .
- Voeg `storage_account_name` een waarde toe aan het configuratieblok.
- Voeg `container_name` een waarde toe aan het configuratieblok.
- Voeg `key` een waarde toe aan het configuratieblok.

In het volgende voorbeeld wordt een terraform back-end geconfigureerd en wordt een Azure-brongroep maakt.

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

U het statusbestand nu vinden in de blob met Azure Storage.

## <a name="state-locking"></a>Staatvergrendeling

Azure Storage-blobs worden automatisch vergrendeld voordat een bewerking wordt uitgevoerd die status schrijft. Dit patroon voorkomt gelijktijdige statusbewerkingen, die corruptie kunnen veroorzaken. 

Zie [Staatsvergrendeling](https://www.terraform.io/docs/state/locking.html) in de Terraform-documentatie voor meer informatie.

U het slot zien wanneer u de blob onderzoekt via de Azure-portal of andere Azure-beheertooling.

![Azure blob met vergrendeling](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Versleuteling 'at rest'

Gegevens die zijn opgeslagen in een Azure-blob, worden versleuteld voordat ze worden gehandhaafd. Indien nodig haalt Terraform de staat uit de back-end en slaat deze op in het lokale geheugen. Met behulp van dit patroon wordt de status nooit naar uw lokale schijf geschreven.

Zie [Azure Storage-serviceversleuteling voor gegevens in rust](../storage/common/storage-service-encryption.md)voor meer informatie over Azure Storage-versleuteling.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Meer informatie over het gebruik van Terraform in Azure](/azure/terraform)
