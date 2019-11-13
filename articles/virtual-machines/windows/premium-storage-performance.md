---
title: "Azure Premium Storage: ontwerpen voor prestaties op Windows-Vm's | Microsoft Docs"
description: Ontwerp krachtige toepassingen met Azure Premium Storage. Premium Storage biedt schijf ondersteuning met hoge prestaties en lage latentie voor I/O-intensieve workloads die worden uitgevoerd op Azure Virtual Machines.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 073b0f0b0078f8e1159fad5e4eb6a70543c81700
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012368"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium-opslag: ontwerpen voor hoge prestaties
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Soms is het een probleem met de prestaties van een schijf. In deze situaties moet u de [netwerk prestaties](../../virtual-network/virtual-network-optimize-network-bandwidth.md)optimaliseren.
>
> Als u een bench Mark-schijf wilt maken, raadpleegt u ons artikel over de [benchmarking van een schijf](disks-benchmarks.md).
>
> Als uw virtuele machine versneld netwerken ondersteunt, moet u ervoor zorgen dat deze is ingeschakeld. Als deze optie niet is ingeschakeld, kunt u deze inschakelen op al geïmplementeerde Vm's in [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) en [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Voordat u begint Premium Storage, lees dan eerst de [Select a Azure-schijf type voor IaaS vm's](disks-types.md) en [Azure Storage schaal baarheid en prestatie doelen voor opslag accounts](../../storage/common/storage-scalability-targets.md).

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Als u een bench Mark-schijf wilt maken, raadpleegt u ons artikel over de [benchmarking van een schijf](disks-benchmarks.md).

Meer informatie over de beschik bare schijf typen: [Selecteer een schijf type](disks-types.md)  

Lees voor SQL Server gebruikers artikelen over best practices voor prestaties voor SQL Server:

* [Aanbevolen procedures voor de prestaties van SQL Server in azure Virtual Machines](sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Storage biedt de beste prestaties voor SQL Server in azure VM](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)