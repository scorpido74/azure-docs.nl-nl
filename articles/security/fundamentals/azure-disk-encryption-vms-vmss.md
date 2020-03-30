---
title: Azure-schijfversleuteling voor virtuele machines en virtuele machineschaalsets
description: In dit artikel vindt u een overzicht van Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: c881b2b9743766e4d35e6cb05f6f3469803850bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062127"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Azure-schijfversleuteling voor virtuele machines en virtuele machineschaalsets

Azure Disk-versleuteling kan worden toegepast op zowel virtuele Linux- als Windows-machines, evenals op virtuele machineschaalsets. 

## <a name="linux-virtual-machines"></a>Virtuele Linux-machines

De volgende artikelen bieden richtlijnen voor het versleutelen van Linux virtuele machines.

### <a name="current-version-of-azure-disk-encryption"></a>Huidige versie van Azure Disk Encryption

- [Overzicht van Azure Disk Encryption voor Linux-VM's](../../virtual-machines/linux/disk-encryption-overview.md)
- [Azure Disk Encryption-scenario's voor Linux-VM's](../../virtual-machines/linux/disk-encryption-linux.md)
- [Een Linux-VM maken en versleutelen met behulp van Azure CLI](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Een Linux-VM maken en versleutelen met behulp van Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Een Linux-VM maken en versleutelen met behulp van Azure Portal](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Azure Disk Encryption Extension Schema voor Linux](../../virtual-machines/extensions/azure-disk-enc-linux.md)
- [Een sleutelkluis voor Azure Disk Encryption maken en configureren](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Voorbeeldscripts voor Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Problemen met Azure Disk Encryption oplossen](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Veelgestelde vragen over Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure-schijfversleuteling met Azure AD (vorige versie)

- [Overzicht van Azure Disk Encryption met Azure AD voor Virtuele Linux-machines](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Azure-schijfversleuteling met Azure AD-scenario's op Linux VM's](../../virtual-machines/linux/disk-encryption-linux.md)
- [Een sleutelkluis voor Azure-schijfversleuteling maken en configureren met Azure AD (vorige release)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Virtuele Windows-machines

De volgende artikelen bieden richtlijnen voor het versleutelen van virtuele Windows-machines.

### <a name="current-version-of-azure-disk-encryption"></a>Huidige versie van Azure Disk Encryption

- [Overzicht van Azure Disk Encryption voor Windows-VM's](../../virtual-machines/windows/disk-encryption-overview.md)
- [Azure Disk Encryption-scenario's voor Windows-VM's](../../virtual-machines/windows/disk-encryption-windows.md)
- [Een virtuele Windows-machine maken en versleutelen met behulp van Azure CLI](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Een virtuele Windows-machine maken en versleutelen met behulp van Azure PowerShell](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Een virtuele Windows-machine maken en versleutelen met behulp van de Azure-portal](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Azure Disk Encryption Extension Schema voor Windows](../../virtual-machines/extensions/azure-disk-enc-windows.md)
- [Een sleutelkluis voor Azure Disk Encryption maken en configureren](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Voorbeeldscripts voor Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Problemen met Azure Disk Encryption oplossen](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Veelgestelde vragen over Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure-schijfversleuteling met Azure AD (vorige versie)

- [Overzicht van Azure Disk Encryption met Azure AD voor virtuele Windows-machines](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Azure-schijfversleuteling met Azure AD-scenario's op Windows VM's](../../virtual-machines/windows/disk-encryption-windows.md)
- [Een sleutelkluis voor Azure-schijfversleuteling maken en configureren met Azure AD (vorige release)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>Virtuele-machineschaalsets

De volgende artikelen bieden richtlijnen voor het versleutelen van virtuele machineschaalsets.

- [Overzicht van Azure Disk Encryption voor virtuele machineschaalsets](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Een virtuele machineschaalsets versleutelen met de Azure CLI](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Een virtuele machineschaalsets versleutelen met Azure Powershell](../../virtual-machine-scale-sets/disk-encryption-powershell.md).
- [Een virtuele machineschaalsets versleutelen met Azure Resource Manager](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Een sleutelkluis maken en configureren voor Azure Disk Encryption](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [Azure-schijfversleuteling gebruiken met detectie van extensie voor virtuele machineschaal](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure-versleuteling](encryption-overview.md)
- [Gegevensversleuteling in rust](encryption-atrest.md)
- [Aanbevolen procedures voor gegevensbeveiliging en -versleuteling](data-encryption-best-practices.md)
