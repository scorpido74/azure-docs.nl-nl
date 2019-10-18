---
title: Azure Disk Encryption voor Virtual Machine Scale Sets inschakelen
description: In dit artikel vindt u instructies voor het inschakelen van Microsoft Azure schijf versleuteling voor Virtual Machine Scale Sets
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: article
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 69197b9c0d2a60e1833d03a3f12802221a8307e2
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530834"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Azure Disk Encryption voor Virtual Machine Scale Sets

Azure Disk Encryption biedt volume versleuteling voor het besturings systeem en de gegevens schijven van uw virtuele machines, waardoor uw gegevens worden beschermd en beveiligd om te voldoen aan de verplichtingen van de organisatie beveiliging en naleving. Zie [Azure Disk Encryption: virtuele Linux-machines](../virtual-machines/linux/disk-encryption-overview.md) en- [Azure Disk Encryption: Windows-vm's](../virtual-machines/windows/disk-encryption-overview.md) voor meer informatie.  

Azure Disk Encryption kan ook worden toegepast op schaal sets voor virtuele Windows-en Linux-machines, in deze gevallen:
- Schaal sets die zijn gemaakt met Managed disks. Azure Disk Encryption wordt niet ondersteund voor een systeem eigen (of niet-beheerde) schijf schaal sets.
- Besturings systeem-en gegevens volumes in Windows-schaal sets.
- Gegevens volumes in Linux-schaal sets. Versleuteling van de besturingssysteem schijf wordt op dit moment niet ondersteund voor Linux-schaal sets.

In slechts enkele minuten kunt u de basis principes van Azure Disk Encryption voor virtuele-machine schaal sets [versleutelen met de Azure cli](disk-encryption-cli.md) of het [versleutelen van een virtuele-machine schaal sets met de zelf studies van Azure cli](disk-encryption-powershell.md) .

## <a name="next-steps"></a>Volgende stappen

- [Een schaal sets voor virtuele machines versleutelen met behulp van de Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Een sleutel kluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md)
- [Azure Disk Encryption gebruiken met uitbreidings reeksen voor de schaalset voor virtuele machines](disk-encryption-extension-sequencing.md)