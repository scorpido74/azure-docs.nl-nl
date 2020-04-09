---
title: Azure Key Vault instellen voor Linux VM's
description: Key Vault instellen voor gebruik met een virtuele azure resource manager met de Azure CLI.
author: mimckitt
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/24/2017
ms.author: mimckitt
ms.openlocfilehash: 59b11d22f054a98fe176e4393843606bd01cc872
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879476"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Key Vault instellen voor virtuele machines met de Azure CLI

In de Azure Resource Manager-stack worden geheimen/certificaten gemodelleerd als resources die worden geleverd door Key Vault. Zie Wat is Azure Key Vault voor meer informatie over Azure Key [Vault?](../../key-vault/key-vault-overview.md) Als U Key Vault wilt gebruiken met Azure Resource Manager VM's, moet de eigenschap *EnabledForDeployment* op Key Vault worden ingesteld op true. In dit artikel ziet u hoe u Key Vault instelt voor gebruik met Virtuele Azure-machines (VM's) met behulp van de Azure CLI. 

Om deze stappen uit te voeren, moet u de nieuwste [Azure CLI](/cli/azure/install-az-cli2) geïnstalleerd en ingelogd op een Azure-account met behulp van [az login](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Een sleutelkluis maken
Maak een sleutelkluis en wijs het implementatiebeleid toe met [het maken van AZ KeyVault.](/cli/azure/keyvault) In het volgende voorbeeld `myKeyVault` wordt `myResourceGroup` een sleutelkluis gebenoemd in de resourcegroep:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Een sleutelkluis bijwerken voor gebruik met VM's
Stel het implementatiebeleid in op een bestaande sleutelkluis met [de AZ KeyVault-update](/cli/azure/keyvault). Met de volgende updates `myKeyVault` wordt `myResourceGroup` de sleutelkluis met de naam in de brongroep bijgewerkt:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Sjablonen gebruiken om Key Vault in te stellen
Wanneer u een sjabloon gebruikt, `enabledForDeployment` moet `true` u de eigenschap als volgt instellen op de Key Vault-bron:

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Zie [Een sleutelkluis maken](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)voor andere opties die u configureren wanneer u een sleutelkluis maakt met behulp van sjablonen.
