---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: e479f2376668a2fc3824e733996c94cfab04c9ec
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468648"
---
## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met de opdracht [az group create](/cli/azure/group). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```azurecli-interactive
az group create \
    --name <resource-group-name> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Maak een opslagaccount

Maak een algemeen opslagaccount met de opdracht [az storage account create](/cli/azure/storage/account). Het algemeen opslagaccount kan voor alle vier de services worden gebruikt: blobs, bestanden, tabellen en wachtrijen.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group <resource-group-name> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Opslagaccountreferenties opgeven

Voor de meeste opdrachten in deze zelfstudie heeft Azure CLI uw opslagaccountreferenties nodig. Een van de eenvoudigste manieren om dit te regelen, is door de omgevingsvariabelen `AZURE_STORAGE_ACCOUNT` en `AZURE_STORAGE_KEY` in te stellen.

> [!NOTE]
> In dit artikel wordt beschreven hoe u omgevings variabelen kunt instellen met behulp van bash. Voor andere omgevingen zijn mogelijk syntaxis wijzigingen vereist.

Geef eerst uw opslag account sleutels weer met behulp van de opdracht [AZ Storage account Keys List](/cli/azure/storage/account/keys) . Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account keys list \
    --account-name <account-name> \
    --resource-group <resource-group-name> \
    --output table
```

Stel nu de omgevingsvariabelen `AZURE_STORAGE_ACCOUNT` en `AZURE_STORAGE_KEY` in. U kunt dit doen in de bash-shell met behulp van de opdracht `export`. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen punt haken te vervangen door uw eigen waarden:

```bash
export AZURE_STORAGE_ACCOUNT="<account-name>"
export AZURE_STORAGE_KEY="<account-key>"
```

Zie [toegangs sleutels voor opslag accounts beheren](../articles/storage/common/storage-account-keys-manage.md)voor meer informatie over het ophalen van de toegangs sleutels voor het account met behulp van de Azure Portal.