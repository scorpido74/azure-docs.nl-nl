---
title: Azure Key Vault instellen voor virtuele Linux-machines
description: Key Vault instellen voor gebruik met een Azure Resource Manager virtuele machine met de Azure CLI.
author: singhkays
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/24/2017
ms.author: kasing
ms.openlocfilehash: c9659c38e349bd62f22ed0d0839d97651b1b3b66
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944823"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Key Vault instellen voor virtuele machines met de Azure CLI

In de Azure Resource Manager stack worden geheimen/certificaten gemodelleerd als bronnen die door Key Vault worden verschaft. Zie [Wat is Azure Key Vault?](../../key-vault/key-vault-overview.md) voor meer informatie over Azure Key Vault. Als Key Vault met Azure Resource Manager Vm's wilt gebruiken, moet de eigenschap *EnabledForDeployment* op Key Vault worden ingesteld op True. In dit artikel wordt beschreven hoe u Key Vault instelt voor gebruik met Azure virtual machines (Vm's) met behulp van de Azure CLI. 

Als u deze stappen wilt uitvoeren, moet u de nieuwste [Azure cli](/cli/azure/install-az-cli2) installeren en u aanmelden bij een Azure-account met [AZ login](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Een sleutelkluis maken
Maak een sleutel kluis en wijs het implementatie beleid toe met [AZ Key kluis Create](/cli/azure/keyvault). In het volgende voor beeld wordt een sleutel kluis gemaakt met de naam `myKeyVault` in de `myResourceGroup` resource groep:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Een Key Vault bijwerken voor gebruik met Vm's
Stel het implementatie beleid in op een bestaande sleutel kluis met [AZ Key kluis update](/cli/azure/keyvault). In het volgende wordt de sleutel kluis met de naam `myKeyVault` in de resource groep `myResourceGroup` bijgewerkt:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Sjablonen gebruiken om Key Vault in te stellen
Wanneer u een sjabloon gebruikt, moet u de eigenschap `enabledForDeployment` als volgt instellen op `true` voor de Key Vault resource:

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
Zie [een sleutel kluis maken](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)voor andere opties die u kunt configureren wanneer u een Key Vault maakt met behulp van sjablonen.
