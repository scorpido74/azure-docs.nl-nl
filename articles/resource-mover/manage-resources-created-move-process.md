---
title: Resources beheren die zijn gemaakt tijdens het verplaatsen van de VM in azure resource-overwerkers
description: Meer informatie over het beheren van resources die zijn gemaakt tijdens het verplaatsen van de VM in azure resource-overwerkers
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 4ff1905eb164d004af69ce5b0df3278bf3a46884
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89670386"
---
# <a name="manage-resources-created-for-the-vm-move"></a>Resources beheren die zijn gemaakt voor het verplaatsen van de VM

In dit artikel wordt beschreven hoe u resources beheert die expliciet door [Azure resource](overview.md) Facility worden gemaakt om het VM-verplaatsings proces te vergemakkelijken. 

Na het verplaatsen van Vm's in verschillende regio's, is er een aantal resources gemaakt door middel van resource-overschakeling die hand matig moet worden opgeruimd.

## <a name="delete-resources-created-for-vm-move"></a>Resources verwijderen die zijn gemaakt voor het verplaatsen van de VM

Verwijder hand matig de verzameling verplaatsen en Site Recovery resources die zijn gemaakt voor het verplaatsen van de virtuele machine.

1. Controleer de resources in de resource groep van waaruit u virtuele machines hebt verplaatst ```RegionMoveRG-<sourceregion>-<target-region>``` .
2. Controleer of de virtuele machine en alle andere bron bronnen in de verplaatsings verzameling zijn verplaatst/verwijderd. Dit zorgt ervoor dat er geen resources in behandeling zijn.
2. Verwijder deze resources.

    - De naam van de verzameling die u wilt verplaatsen ```movecollection-<sourceregion>-<target-region>``` .
    - De naam van het cache-opslag account is ```resmovecache<guid>```
    - De kluis naam is ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Volgende stappen

Verplaats [een virtuele machine](tutorial-move-region-virtual-machines.md) naar een andere regio met resource-overschakeling.