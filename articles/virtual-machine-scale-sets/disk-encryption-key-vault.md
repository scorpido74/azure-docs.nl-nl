---
title: Een sleutel kluis voor Azure Disk Encryption maken en configureren
description: In dit artikel worden de stappen beschreven voor het maken en configureren van een sleutel kluis voor gebruik met Azure Disk Encryption
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: eec5b42da709ab5e79da42f11600f6ffc81d247e
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279004"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Een sleutel kluis voor Azure Disk Encryption maken en configureren

Azure Disk Encryption gebruikt Azure Key Vault om sleutels en geheimen voor schijf versleuteling te beheren en te beheren.  Zie voor meer informatie over sleutelkluizen [aan de slag met Azure Key Vault](../key-vault/key-vault-get-started.md) en [uw key vault beveiligen](../key-vault/key-vault-secure-your-key-vault.md).

Een sleutel kluis maken en configureren voor gebruik met Azure Disk Encryption bestaat uit drie stappen:

1. Een resource groep maken, indien nodig.
2. Een sleutel kluis maken. 
3. Geavanceerd toegangs beleid voor sleutel kluis instellen.

Deze stappen worden geïllustreerd in de volgende Quick starts:

U kunt eventueel ook een sleutel versleutelings sleutel genereren of importeren (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Hulpprogram ma's installeren en verbinding maken met Azure

U kunt de stappen in dit artikel uitvoeren met de [Azure cli](/cli/azure/), de [Azure PowerShell AZ-module](/powershell/azure/overview)of de [Azure Portal](https://portal.azure.com).

### <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Voordat u de Azure CLI of Azure PowerShell gebruikt, moet u eerst verbinding maken met uw Azure-abonnement. U doet dit door u aan te [melden met Azure cli](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), u aan te [melden met Azure Power shell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)of uw referenties aan de Azure portal toe te voegen wanneer u hierom wordt gevraagd.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
- [Een schaalset voor virtuele machines versleutelen met behulp van Azure CLI](disk-encryption-cli.md)
- [Een schaal sets voor virtuele machines versleutelen met behulp van de Azure PowerShell](disk-encryption-powershell.md)
