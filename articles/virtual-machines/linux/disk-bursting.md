---
title: Bursting van beheerde schijven
description: Meer informatie over schijf bursting voor Azure-schijven en schijf bursting voor virtuele Azure-machines
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: baabad550f5e6b0ae39936fc182e6d9789d189d8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650938"
---
# <a name="disk-bursting"></a>Disk Bursting
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Burstisatie op het niveau van de virtuele machine
Ondersteuning voor burstisatie op VM-niveau is ingeschakeld in alle regio's in de open bare Cloud op de volgende ondersteunde grootten: 
- [Lsv2-serie](../lsv2-series.md)

Bursting is standaard ingeschakeld voor virtuele machines die dit ondersteunen.

## <a name="disk-level-bursting"></a>Burstisatie op schijf niveau
Bursting is ook beschikbaar op onze [Premium-ssd's](disks-types.md#premium-ssd) voor de schijf grootten P20 en kleiner in alle regio's in azure Public-, Government-en China-Clouds. Schijf bursting is standaard ingeschakeld voor nieuwe implementaties van de schijf grootten die dit ondersteunen. Bestaande schijf grootten, als deze schijf bursting ondersteunen, kunnen bursting op een van de volgende manieren inschakelen: 
- **De virtuele machine opnieuw opstarten** 
- **De schijf loskoppelen en opnieuw koppelen**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
