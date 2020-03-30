---
title: Een virtuele machineschaalset maken en versleutelen met Azure Resource Manager-sjablonen
description: In deze quickstart leert u hoe u Azure Resource Manager-sjablonen gebruiken om een virtuele machineschaalset te maken en te versleutelen
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.date: 10/10/2019
ms.openlocfilehash: aeba9aee8dd10b1245fb0a1e1cc98a1951382c72
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "72529998"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Schaalsets voor virtuele machines versleutelen met Azure Resource Manager

U linux-eenvoudigmaquettesets voor virtuele machines versleutelen of decoderen met Azure Resource Manager-sjablonen.

## <a name="deploying-templates"></a>Sjablonen implementeren

Selecteer eerst de sjabloon die bij uw scenario past.

- [Schijfversleuteling inschakelen op een met Linux-schijfschaalset](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Schijfversleuteling inschakelen op een met windows-schijfschaalset voor virtuele machines](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Implementeer een virtuele machine schaal set van Linux VM's met een jumpbox en maakt encryptie op Linux virtuele machine schaal sets](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Een virtuele machineschaalset van Windows VM's implementeren met een jumpbox en maakt versleuteling mogelijk op windows-schaalsets voor virtuele machines](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Schijfversleuteling uitschakelen op een met Linux-schijfschaalset](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Schijfversleuteling uitschakelen op een met windows virtuele machineschaalset](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Volg nu deze stappen:

     1. Click **Deploy to Azure**.
     2. Fill in the required fields then agree to the terms and conditions.
     3. Click **Purchase** to deploy the template.

## <a name="next-steps"></a>Volgende stappen

- [Azure-schijfversleuteling voor virtuele machineschaalsets](disk-encryption-overview.md)
- [Een virtuele machineschaalsets versleutelen met de Azure CLI](disk-encryption-cli.md)
- [Een virtuele machineschaalsets versleutelen met de Azure PowerShell](disk-encryption-powershell.md)
- [Een sleutelkluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md)
- [Azure-schijfversleuteling gebruiken met detectie van extensie voor virtuele machineschaal](disk-encryption-extension-sequencing.md)