---
title: Bursting van beheerde schijven
description: Meer informatie over schijf bursting voor Azure-schijven en schijf bursting voor virtuele Azure-machines
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 547834542b605b226ebffd68e05296ee847dc6de
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974325"
---
# <a name="disk-bursting"></a>Disk Bursting
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Burstisatie op het niveau van de virtuele machine
Ondersteuning voor burstisatie op VM-niveau is ingeschakeld in alle regio's in de open bare Cloud op de volgende ondersteunde grootten: 
- [Lsv2-serie](../lsv2-series.md)

Burstisatie op VM-niveau is ook beschikbaar in West-Centraal VS voor de volgende ondersteunde grootten:
- [Dsv3-serie](../dv3-dsv3-series.md)
- [Esv3-serie](../ev3-esv3-series.md)

Bursting is standaard ingeschakeld voor virtuele machines die dit ondersteunen.

## <a name="disk-level-bursting"></a>Burstisatie op schijf niveau
Bursting is ook beschikbaar op onze [Premium-ssd's](../disks-types.md#premium-ssd) voor schijf grootten P20 en kleiner in alle regio's. Schijf bursting is standaard ingeschakeld voor nieuwe implementaties van de schijf grootten die dit ondersteunen. Bestaande schijf grootten, als deze schijf bursting ondersteunen, kunnen bursting op een van de volgende manieren inschakelen: 
- **De virtuele machine opnieuw opstarten** 
- **De schijf loskoppelen en opnieuw koppelen**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]