---
title: Een sleutelkluis voor Azure Disk Encryption maken en configureren
description: In dit artikel vindt u stappen voor het maken en configureren van een sleutelkluis voor gebruik met Azure Disk Encryption
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: eec5b42da709ab5e79da42f11600f6ffc81d247e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279004"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Een sleutelkluis voor Azure Disk Encryption maken en configureren

Azure Disk Encryption gebruikt Azure Key Vault om schijfversleutelingssleutels en -geheimen te beheren en te beheren.  Zie [Aan de slag met Azure Key Vault](../key-vault/key-vault-get-started.md) en Uw [sleutelkluis beveiligen](../key-vault/key-vault-secure-your-key-vault.md)voor meer informatie over sleutelkluizen.

Het maken en configureren van een sleutelkluis voor gebruik met Azure Disk Encryption omvat drie stappen:

1. Indien nodig een resourcegroep maken.
2. Het creëren van een sleutelkluis. 
3. Het instellen van geavanceerde toegangsbeleidsregels voor belangrijke vaults.

Deze stappen worden geïllustreerd in de volgende snelstarts:

U ook, indien u dat wenst, een sleutelversleutelingssleutel (KEK) genereren of importeren.

## <a name="install-tools-and-connect-to-azure"></a>Hulpprogramma's installeren en verbinding maken met Azure

De stappen in dit artikel kunnen worden voltooid met de [Azure CLI,](/cli/azure/)de [Azure PowerShell Az-module](/powershell/azure/overview)of de [Azure-portal](https://portal.azure.com).

### <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Voordat u de Azure CLI of Azure PowerShell gebruikt, moet u eerst verbinding maken met uw Azure-abonnement. U doet dit door [u aan te melden bij Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), zich aan te melden bij Azure [Powershell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)of uw referenties aan de Azure-portal te leveren wanneer daarom wordt gevraagd.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
- [Een virtuele machineschaalsets versleutelen met de Azure CLI](disk-encryption-cli.md)
- [Een virtuele machineschaalsets versleutelen met de Azure PowerShell](disk-encryption-powershell.md)
