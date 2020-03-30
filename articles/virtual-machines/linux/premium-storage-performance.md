---
title: "Azure Premium Storage: ontwerp voor prestaties op Linux VM's | Microsoft Documenten"
description: Ontwerp krachtige toepassingen met Azure premium SSD-beheerde schijven. Premium Storage biedt krachtige schijfondersteuning met lage latentie voor I/O-intensieve workloads die worden uitgevoerd op Azure Virtual Machines.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9940ee4cfce9721ac65f2b3cf1469e180adfa098
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267142"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure premium storage: ontwerp voor hoge prestaties
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Soms, wat lijkt op een schijf prestaties probleem is eigenlijk een netwerk knelpunt. In deze situaties moet u uw [netwerkprestaties](../../virtual-network/virtual-network-optimize-network-bandwidth.md)optimaliseren.
>
> Als u op zoek bent naar benchmark uw schijf, zie ons artikel over [Benchmarking een schijf](disks-benchmarks.md).
>
> Als uw VM versnelde netwerken ondersteunt, moet u ervoor zorgen dat deze is ingeschakeld. Als deze niet is ingeschakeld, u deze inschakelen op reeds geïmplementeerde VM's op zowel [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) als [Linux.](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)

Lees voordat u begint, als u nieuw bent in Premium Storage, eerst het [type Azure-schijftype selecteren voor IaaS-vm's](disks-types.md) en [schaalbaarheidsdoelen voor blob-opslagaccounts met een premium pagina.](../../storage/blobs/scalability-targets-premium-page-blobs.md)


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Als u op zoek bent naar benchmark uw schijf, zie ons artikel over [Benchmarking een schijf](disks-benchmarks.md).

Meer informatie over de beschikbare schijftypen: [Een schijftype selecteren](disks-types.md)  

Lees voor SQL Server-gebruikers artikelen over aanbevolen procedures voor prestaties voor SQL Server:

* [Aanbevolen procedures voor prestaties voor SQL Server in Azure Virtual Machines](../windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Storage biedt de hoogste prestaties voor SQL Server in Azure VM](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)