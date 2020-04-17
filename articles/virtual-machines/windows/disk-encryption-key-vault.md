---
title: Een sleutelkluis voor Azure Disk Encryption maken en configureren
description: In dit artikel vindt u stappen voor het maken en configureren van een sleutelkluis voor gebruik met Azure Disk Encryption
ms.service: virtual-machines
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 9fe11c7d83806835c8a4882bf2300efc82e37ab2
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458927"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Een sleutelkluis voor Azure Disk Encryption maken en configureren

Azure Disk Encryption gebruikt Azure Key Vault om schijfversleutelingssleutels en -geheimen te beheren en te beheren.  Zie [Aan de slag met Azure Key Vault](../../key-vault/key-vault-get-started.md) en Uw [sleutelkluis beveiligen](../../key-vault/general/secure-your-key-vault.md)voor meer informatie over sleutelkluizen. 

> [!WARNING]
> - Als u azure-schijfversleuteling eerder met Azure AD hebt gebruikt om een vm te versleutelen, moet u deze optie blijven gebruiken om uw vm te versleutelen. Zie [Een sleutelkluis voor Azure-schijfversleuteling maken en configureren met Azure AD (vorige release)](disk-encryption-key-vault-aad.md) voor meer informatie.

Het maken en configureren van een sleutelkluis voor gebruik met Azure Disk Encryption omvat drie stappen:

1. Indien nodig een resourcegroep maken.
2. Het creëren van een sleutelkluis. 
3. Het instellen van geavanceerde toegangsbeleidsregels voor belangrijke vaults.

Deze stappen worden geïllustreerd in de volgende snelstarts:

- [Een virtuele Windows-machine maken en versleutelen met behulp van Azure CLI](disk-encryption-cli-quickstart.md)
- [Een virtuele Windows-machine maken en versleutelen met behulp van Azure PowerShell](disk-encryption-cli-quickstart.md)

U ook, indien u dat wenst, een sleutelversleutelingssleutel (KEK) genereren of importeren.

> [!Note]
> De stappen in dit artikel zijn geautomatiseerd in de [vereisten voor Azure Disk Encryption CLI-script](https://github.com/ejarvi/ade-cli-getting-started) en Azure Disk [Encryption-vereisten PowerShell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Hulpprogramma's installeren en verbinding maken met Azure

De stappen in dit artikel kunnen worden voltooid met de [Azure CLI,](/cli/azure/)de [Azure PowerShell Az-module](/powershell/azure/overview)of de [Azure-portal](https://portal.azure.com).

Hoewel de portal toegankelijk is via uw browser, vereisen Azure CLI en Azure PowerShell lokale installatie. zie [Azure Disk Encryption voor Windows: Hulpprogramma's installeren](disk-encryption-windows.md#install-tools-and-connect-to-azure) voor meer informatie.

### <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Voordat u de Azure CLI of Azure PowerShell gebruikt, moet u eerst verbinding maken met uw Azure-abonnement. U doet dit door [u aan te melden bij Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), zich aan te melden bij Azure [Powershell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)of uw referenties aan de Azure-portal te leveren wanneer daarom wordt gevraagd.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Volgende stappen

- [Azure Disk Encryption-vereisten voor CLI-script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption-vereisten Voor PowerShell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Azure [Disk Encryption-scenario's op Windows VM's](disk-encryption-windows.md)
- Meer informatie over het [oplossen van problemen met Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Lees de [voorbeeldscripts azure diskversleuteling](disk-encryption-sample-scripts.md)
