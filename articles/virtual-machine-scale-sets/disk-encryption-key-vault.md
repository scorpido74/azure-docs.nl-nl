---
title: Een sleutelkluis voor Azure Disk Encryption maken en configureren
description: In dit artikel worden de stappen beschreven voor het maken en configureren van een sleutel kluis voor gebruik met Azure Disk Encryption
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 30fa6b910c0241621c2b2cdae9bb9a164f27cedb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81454540"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Een sleutelkluis voor Azure Disk Encryption maken en configureren

Azure Disk Encryption gebruikt Azure Key Vault om sleutels en geheimen voor schijf versleuteling te beheren en te beheren.  Zie [aan de slag met Azure Key Vault](../key-vault/key-vault-get-started.md) en [uw sleutel kluis beveiligen](../key-vault/general/secure-your-key-vault.md)voor meer informatie over sleutel kluizen.

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
