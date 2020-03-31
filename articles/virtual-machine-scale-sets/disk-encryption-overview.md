---
title: Azure-schijfversleuteling inschakelen voor virtuele machineschaalsets
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure Disk Encryption for Virtual Machine Scale Sets
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 36eb83d99af299260ebbbd74fe56d69921ba389b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278973"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Azure-schijfversleuteling voor virtuele machineschaalsets

Azure Disk Encryption biedt volumeversleuteling voor het besturingssysteem en de gegevensschijven van uw virtuele machines, zodat uw gegevens worden beschermd en beschermd om te voldoen aan de verplichtingen op het gebied van organisatiebeveiliging en naleving. Zie [Azure Disk Encryption: Linux VM's](../virtual-machines/linux/disk-encryption-overview.md) en [Azure Disk Encryption: Windows VM's voor](../virtual-machines/windows/disk-encryption-overview.md) meer informatie  

Azure Disk Encryption kan ook worden toegepast op Windows en Linux virtuele machine schaalsets, in deze gevallen:
- Schaalsets gemaakt met beheerde schijven. Azure Disk-versleuteling wordt niet ondersteund voor native (of onbeheerde) schijfwaarden.
- BE- en gegevensvolumes in Windows-schaalsets.
- Datavolumes in Linux schaalsets. OS-schijfversleuteling wordt momenteel NIET ondersteund voor Linux-schaalsets.

U de basisprincipes van Azure Disk Encryption voor virtuele machineschaalsets in slechts enkele minuten leren met de [schaalsets voor virtuele machines versleutelen met behulp van de azure CLI-](disk-encryption-cli.md) of [de schaalsets voor virtuele machines versleutelen met behulp van de Azure PowerShell-zelfstudies.](disk-encryption-powershell.md)

## <a name="next-steps"></a>Volgende stappen

- [Een virtuele machineschaalsets versleutelen met Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Een sleutelkluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md)
- [Azure-schijfversleuteling gebruiken met detectie van extensie voor virtuele machineschaal](disk-encryption-extension-sequencing.md)
