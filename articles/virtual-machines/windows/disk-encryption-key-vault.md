---
title: Een sleutelkluis voor Azure Disk Encryption maken en configureren
description: In dit artikel worden de stappen beschreven voor het maken en configureren van een sleutel kluis voor gebruik met Azure Disk Encryption
ms.service: virtual-machines
ms.subservice: security
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: f1317989434cca9375c612c8af0525323c6dda3c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82081656"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Een sleutelkluis voor Azure Disk Encryption maken en configureren

Azure Disk Encryption gebruikt Azure Key Vault om sleutels en geheimen voor schijf versleuteling te beheren en te beheren.  Zie [aan de slag met Azure Key Vault](../../key-vault/key-vault-get-started.md) en [uw sleutel kluis beveiligen](../../key-vault/general/secure-your-key-vault.md)voor meer informatie over sleutel kluizen. 

> [!WARNING]
> - Als u eerder Azure Disk Encryption met Azure AD hebt gebruikt om een virtuele machine te versleutelen, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen. Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)](disk-encryption-key-vault-aad.md) voor meer informatie.

Een sleutel kluis maken en configureren voor gebruik met Azure Disk Encryption bestaat uit drie stappen:

1. Een resource groep maken, indien nodig.
2. Een sleutel kluis maken. 
3. Geavanceerd toegangs beleid voor sleutel kluis instellen.

Deze stappen worden geïllustreerd in de volgende Quick starts:

- [Een virtuele Windows-machine maken en versleutelen met behulp van Azure CLI](disk-encryption-cli-quickstart.md)
- [Een virtuele Windows-machine maken en versleutelen met behulp van Azure PowerShell](disk-encryption-cli-quickstart.md)

U kunt eventueel ook een sleutel versleutelings sleutel genereren of importeren (KEK).

> [!Note]
> De stappen in dit artikel zijn geautomatiseerd in het [Azure Disk Encryption vereisten CLI-script](https://github.com/ejarvi/ade-cli-getting-started) en [Azure Disk Encryption vereisten Power shell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Hulpprogram ma's installeren en verbinding maken met Azure

U kunt de stappen in dit artikel uitvoeren met de [Azure cli](/cli/azure/), de [Azure PowerShell AZ-module](/powershell/azure/overview)of de [Azure Portal](https://portal.azure.com).

Terwijl de portal toegankelijk is via uw browser, hebben Azure CLI en Azure PowerShell lokale installatie nodig. Zie [Azure Disk Encryption voor Windows: hulpprogram Ma's installeren](disk-encryption-windows.md#install-tools-and-connect-to-azure) voor meer informatie.

### <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Voordat u de Azure CLI of Azure PowerShell gebruikt, moet u eerst verbinding maken met uw Azure-abonnement. U doet dit door u aan te [melden met Azure cli](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), u aan te [melden met Azure Power shell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)of uw referenties aan de Azure portal toe te voegen wanneer u hierom wordt gevraagd.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Volgende stappen

- [SysteemAzure Disk Encryption vereisten CLI-script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption van vereisten Power shell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Azure Disk Encryption scenario's voor Windows-vm's](disk-encryption-windows.md) leren
- Meer informatie over het [oplossen van Azure Disk Encryption](disk-encryption-troubleshooting.md)
- De [voorbeeld scripts van Azure Disk Encryption](disk-encryption-sample-scripts.md) lezen
