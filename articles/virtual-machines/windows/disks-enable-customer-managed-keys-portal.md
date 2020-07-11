---
title: Gebruik de Azure Portal om door de klant beheerde sleutels met SSE-beheerde schijven in te scha kelen
description: Gebruik de Azure Portal om versleuteling aan de server zijde in te scha kelen met door de klant beheerde sleutels op uw beheerde schijven.
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 4e3699e1fab928db5a466073c411b701ffc1a51d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86235902"
---
# <a name="enable-customer-managed-keys-with-server-side-encryption---managed-disks---portal"></a>Door de klant beheerde sleutels met versleuteling aan server zijde inschakelen-Portal

Met Azure Disk Storage kunt u uw eigen sleutels beheren wanneer u gebruikmaakt van SSE (server side Encryption) voor beheerde schijven, indien gewenst. Zie de sectie door de [klant beheerde sleutels](disk-encryption.md#customer-managed-keys) in ons artikel over schijf versleuteling voor conceptuele informatie over SSE met door de klant beheerde sleutels en andere beheerde schijf versleutelings typen.

## <a name="restrictions"></a>Beperkingen

De door de klant beheerde sleutels hebben nu de volgende beperkingen:

- Als deze functie is ingeschakeld voor uw schijf, kunt u deze niet uitschakelen.
    Als u dit wilt omzeilen, moet u [alle gegevens](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) naar een volledig andere beheerde schijf kopiëren die geen door de klant beheerde sleutels gebruikt.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

De volgende secties bevatten informatie over het inschakelen en gebruiken van door de klant beheerde sleutels voor beheerde schijven:

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn gebaseerd op beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Wanneer u door de klant beheerde sleutels configureert, wordt er automatisch een beheerde identiteit toegewezen aan uw resources onder de kaften. Als u het abonnement, de resource groep of de beheerde schijf vervolgens verplaatst van een Azure AD-Directory naar een andere, wordt de beheerde identiteit die is gekoppeld aan de beheerde schijven, niet overgebracht naar de nieuwe Tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie [een abonnement overdragen tussen Azure AD-mappen](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Bekijk de Azure Resource Manager sjablonen voor het maken van versleutelde schijven met door de klant beheerde sleutels](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Wat is Azure Key Vault?](../../key-vault/general/overview.md)
- [Computers repliceren met door de klant beheerde sleutels ingeschakelde schijven](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Herstel na nood geval instellen voor virtuele VMware-machines in azure met Power shell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Herstel na nood geval instellen op Azure voor Hyper-V-Vm's met behulp van Power shell en Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)