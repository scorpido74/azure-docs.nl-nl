---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/12/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 64aa01995460837c820c90010d7c4e3f3d78e6a2
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79300230"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Regionale beschikbaarheid
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portal

Als u incrementele moment opnamen wilt maken en configureren in de globale Azure Portal, moet u de volgende koppeling gebruiken: [https://aka.ms/incrementalsnapshots](https://aka.ms/incrementalsnapshots). Het maken van een incrementele moment opname is nog niet beschikbaar in de globale Azure Portal.

1. Meld u aan bij de [Azure Portal](https://aka.ms/incrementalsnapshots) met de beschik bare koppeling en navigeer naar de schijf die u wilt maken met een moment opname.
1. Selecteer **een moment opname maken** op de schijf

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text=" ":::

1. Selecteer de resource groep die u wilt gebruiken en voer een naam in.
1. Selecteer **stapsgewijs** en selecteer **controleren + maken**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text=" ":::

1. Selecteer **Maken**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text=" ":::

## <a name="next-steps"></a>Volgende stappen

Zie [Azure-Managed disks back-ups kopiëren naar een andere regio met differentiële mogelijkheden van incrementele moment opnamen](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)als u voorbeeld code wilt zien met behulp van de differentiële mogelijkheden van incrementele moment opnamen.
