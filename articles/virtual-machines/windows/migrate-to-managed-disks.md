---
title: Azure VM's migreren naar beheerde schijven
description: Migreren van Virtuele Azure-machines die zijn gemaakt met onbeheerde schijven in opslagaccounts om Beheerde schijven te gebruiken.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e8f2753ac9062803a2d6252eca1829cb0b168f02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921348"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Azure VM's migreren naar beheerde schijven in Azure

Azure Managed Disks vereenvoudigt uw opslagbeheer door dat opslagaccounts niet afzonderlijk hoeven te worden beheerd.  U uw bestaande Azure VM's ook migreren naar beheerde schijven om te profiteren van een betere betrouwbaarheid van VM's in een beschikbaarheidsset. Het zorgt ervoor dat de schijven van verschillende VM's in een beschikbaarheidsset voldoende van elkaar zijn geïsoleerd om één storingspunt te voorkomen. Het plaatst automatisch schijven van verschillende VM's in een beschikbaarheidsset in verschillende opslagschaaleenheden (stempels) die de impact van afzonderlijke storingen in de opslagschaal-eenheid beperkt die zijn veroorzaakt door hardware- en softwarefouten.
Op basis van uw behoeften u kiezen uit vier soorten opslagopties. Zie ons artikel [Een schijftype selecteren](disks-types.md) voor meer informatie over de beschikbare schijftypen

## <a name="migration-scenarios"></a> Migratiescenario's

U migreren naar beheerde schijven in volgende scenario's:

|Scenario  |Artikel  |
|---------|---------|
|Zelfstandige virtuele machines en virtuele machines in een beschikbaarheidsset naar beheerde schijven converteren     |[VM's converteren naar beheerde schijven](convert-unmanaged-to-managed-disks.md)         |
|Eén virtuele machine converteren van klassiek naar Resourcebeheer op beheerde schijven     |[Maak een VM van een klassieke VHD](create-vm-specialized-portal.md)         |
|Alle VM's in een vNet converteren van klassiek naar Resource Manager op beheerde schijven     |[IaaS-resources migreren van klassiek naar Resourcebeheer](migration-classic-resource-manager-ps.md) en [vervolgens een VM converteren van niet-beheerde schijven naar beheerde schijven](convert-unmanaged-to-managed-disks.md)         |
|VM's upgraden met standaard niet-beheerde schijven naar VM's met beheerde premiumschijven     | [Converteer eerst een virtuele Windows-machine van onbeheerde schijven naar beheerde schijven.](convert-unmanaged-to-managed-disks.md) Werk [vervolgens het opslagtype van een beheerde schijf bij](convert-disk-storage.md).         |

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beheerde schijven](managed-disks-overview.md)
- Bekijk de [prijzen voor beheerde schijven](https://azure.microsoft.com/pricing/details/managed-disks/).
