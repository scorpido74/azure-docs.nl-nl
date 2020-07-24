---
title: End-to-end-versleuteling inschakelen met behulp van versleuteling op Azure Portal-beheerde schijven
description: Gebruik versleuteling op host om end-to-end versleuteling in te scha kelen op uw Azure Managed disks.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/23/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 96506e0ca09e9491135faa69d0b690b91ccc8177
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136127"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-portal"></a>End-to-end-versleuteling inschakelen met behulp van versleuteling op host-Azure Portal

Wanneer u versleuteling inschakelt op de host, worden gegevens die op de VM-host zijn opgeslagen, versleuteld op rest en stromen die zijn versleuteld naar de opslag service. Zie [versleuteling bij host-end-to-end-versleuteling voor uw VM-gegevens](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)voor conceptuele informatie over versleuteling op de host, evenals andere beheerde schijf versleutelings typen.

[!INCLUDE [virtual-machines-disks-encryption-at-host-portal](../../../includes/virtual-machines-disks-encryption-at-host-portal.md)]

## <a name="next-steps"></a>Volgende stappen

[Azure Resource Manager-voorbeeldsjablonen](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)