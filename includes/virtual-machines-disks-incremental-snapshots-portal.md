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
ms.openlocfilehash: 8f2e2081ee31e2ce7b21677bd261ed7eda705334
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79485981"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Regionale beschikbaarheid
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portal

Als u incrementele momentopnamen wilt maken en configureren in [https://aka.ms/incrementalsnapshots](https://aka.ms/incrementalsnapshots)de wereldwijde Azure-portal, moet u de volgende koppeling gebruiken: . Incrementele momentopnamecreatie is nog niet beschikbaar in de wereldwijde Azure-portal.

1. Meld u aan bij de [Azure-portal](https://aka.ms/incrementalsnapshots) met de meegeleverde koppeling en navigeer naar de schijf die u wilt momentopnamemaken.
1. Selecteer op uw schijf **Een momentopname maken**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Screenshot. Het blad van uw schijf, met **+Momentopname maken** gemarkeerd, want dat is wat u moet selecteren.":::

1. Selecteer de resourcegroep die u wilt gebruiken en voer een naam in.
1. Selecteer **Incrementeel** en selecteer **Controleren + Maken**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Screenshot. Maak een momentopnameblad, vul de naam in en selecteer incrementeel en maak vervolgens uw momentopname.":::

1. Selecteer **Maken**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Screenshot. Validatiepagina voor uw momentopname, bevestig uw selecties en maak vervolgens de momentopname.":::

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Managed Disks back-ups kopiëren naar een andere regio met differentiële mogelijkheden van incrementele momentopnamen](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)als u voorbeeldcode wilt zien die de differentiële mogelijkheid van incrementele momentopnamen weergeeft.
