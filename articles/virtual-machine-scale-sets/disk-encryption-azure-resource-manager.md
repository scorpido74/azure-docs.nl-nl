---
title: Een Schaalset voor virtuele machines maken en versleutelen met Azure Resource Manager sjablonen
description: In deze Snelstartgids leert u hoe u Azure Resource Manager sjablonen kunt gebruiken om een Schaalset voor virtuele machines te maken en te versleutelen
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.date: 10/10/2019
ms.openlocfilehash: aeba9aee8dd10b1245fb0a1e1cc98a1951382c72
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529998"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Schaal sets voor virtuele machines versleutelen met Azure Resource Manager

U kunt schaal sets voor virtuele Linux-machines versleutelen of ontsleutelen met Azure Resource Manager sjablonen.

## <a name="deploying-templates"></a>Sjablonen implementeren

Selecteer eerst de sjabloon die past bij uw scenario.

- [Schijf versleuteling inschakelen op een actieve Linux-schaalset voor virtuele machines](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Schijf versleuteling inschakelen op een actieve Windows-schaalset voor virtuele machines](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Een schaalset voor virtuele machines met Linux-machines implementeren met een JumpBox en versleuteling inschakelen op schaal sets voor virtuele Linux-machines](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Een virtuele-machine-schaalset van Windows-Vm's implementeren met een JumpBox en versleuteling inschakelen voor virtuele Windows-machine schaal sets](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Schijf versleuteling uitschakelen voor een actieve Linux-schaalset voor virtuele machines](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Schijf versleuteling uitschakelen op een actieve Windows-schaalset voor virtuele machines](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Voer vervolgens de volgende stappen uit:

     1. Click **Deploy to Azure**.
     2. Fill in the required fields then agree to the terms and conditions.
     3. Click **Purchase** to deploy the template.

## <a name="next-steps"></a>Volgende stappen

- [Azure Disk Encryption voor schaal sets voor virtuele machines](disk-encryption-overview.md)
- [Een schaalset voor virtuele machines versleutelen met behulp van Azure CLI](disk-encryption-cli.md)
- [Een schaal sets voor virtuele machines versleutelen met behulp van de Azure PowerShell](disk-encryption-powershell.md)
- [Een sleutel kluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md)
- [Azure Disk Encryption gebruiken met uitbreidings reeksen voor de schaalset voor virtuele machines](disk-encryption-extension-sequencing.md)