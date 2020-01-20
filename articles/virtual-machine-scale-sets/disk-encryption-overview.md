---
title: Azure Disk Encryption voor Virtual Machine Scale Sets inschakelen
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure schijf versleuteling voor Virtual Machine Scale Sets
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 36eb83d99af299260ebbbd74fe56d69921ba389b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278973"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Azure Disk Encryption voor Virtual Machine Scale Sets

Azure Disk Encryption biedt volume versleuteling voor het besturings systeem en de gegevens schijven van uw virtuele machines, waardoor uw gegevens worden beschermd en beveiligd om te voldoen aan de verplichtingen van de organisatie beveiliging en naleving. Zie [Azure Disk Encryption: virtuele Linux-machines](../virtual-machines/linux/disk-encryption-overview.md) en- [Azure Disk Encryption: Windows-vm's](../virtual-machines/windows/disk-encryption-overview.md) voor meer informatie.  

Azure Disk Encryption kan ook worden toegepast op schaal sets voor virtuele Windows-en Linux-machines, in deze gevallen:
- Schaal sets die zijn gemaakt met Managed disks. Azure Disk Encryption wordt niet ondersteund voor een systeem eigen (of niet-beheerde) schijf schaal sets.
- Besturings systeem-en gegevens volumes in Windows-schaal sets.
- Gegevens volumes in Linux-schaal sets. Versleuteling van de besturingssysteem schijf wordt op dit moment niet ondersteund voor Linux-schaal sets.

In slechts enkele minuten kunt u de basis principes van Azure Disk Encryption voor virtuele-machine schaal sets [versleutelen met behulp van de Azure cli](disk-encryption-cli.md) of de [schaal sets van virtuele machines versleutelen met behulp van de Azure PowerShell](disk-encryption-powershell.md) zelf studies.

## <a name="next-steps"></a>Volgende stappen

- [Een schaal sets voor virtuele machines versleutelen met behulp van de Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Een sleutel kluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md)
- [Azure Disk Encryption gebruiken met uitbreidings reeksen voor de schaalset voor virtuele machines](disk-encryption-extension-sequencing.md)
