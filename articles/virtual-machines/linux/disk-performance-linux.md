---
title: Prestaties van virtuele machine en schijf
description: Meer informatie over hoe Vm's en de bijbehorende schijven samen werken in combi natie met prestaties
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 092368bb66784a00d5116da0b6be6513f8ebb261
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518053"
---
# <a name="virtual-machine-and-disk-performance"></a>Prestaties van virtuele machine en schijf
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Virtuele machine niet in cache opgeslagen limieten versus cache
Voor virtuele machines die zijn ingeschakeld voor Premium Storage en Premium Storage-caching, hebben twee verschillende bandbreedte limieten voor opslag. Laten we eens kijken naar de Standard_D8s_v3 virtuele machine als voor beeld. Hier vindt u de documentatie over de [Dsv3-serie](../dv3-dsv3-series.md) en de Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Laten we een benchmark test uitvoeren op deze virtuele machine en schijf combinatie waarmee i/o-activiteiten worden gemaakt. Zie [Bench Mark your application on Azure Disk Storage](disks-benchmarks.md)voor meer informatie over de Bench Mark-opslag-io in Azure. Vanuit het hulp programma benchmarking kunt u zien dat de combi natie van de virtuele machine en schijf 22.800 IOPS kan behaalt:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
