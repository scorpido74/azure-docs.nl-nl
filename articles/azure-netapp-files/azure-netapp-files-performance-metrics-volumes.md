---
title: Aanbevolen prestatiebenchmarktests - Azure NetApp-bestanden
description: Meer informatie over aanbevelingen voor benchmarktests voor volumeprestaties en -statistieken met Azure NetApp-bestanden.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: 8f354152c23dd7ad0413f27585d724f8070ca003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551519"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Aanbevelingen van prestatiebenchmarks voor Azure NetApp Files

In dit artikel vindt u aanbevelingen voor het testen van benchmarks voor volumeprestaties en metrische gegevens met Azure NetApp-bestanden.

## <a name="overview"></a>Overzicht

Om inzicht te krijgen in de prestatiekenmerken van een Azure NetApp Files-volume, u het open-sourcetool [FIO](https://github.com/axboe/fio) gebruiken om een reeks benchmarks uit te voeren om verschillende workloads te simuleren. FIO kan worden geïnstalleerd op zowel Linux- als Windows-besturingssystemen.  Het is een uitstekend hulpmiddel om een snelle momentopname van zowel IOPS als doorvoer voor een volume te krijgen.

### <a name="vm-instance-sizing"></a>VM-instantiegrootte

Voor de beste resultaten moet u ervoor zorgen dat u een vm-exemplaar (virtual machine) gebruikt dat op de juiste grootte is aangepast om de tests uit te voeren. In de volgende voorbeelden wordt een Standard_D32s_v3 instantie gebruikt. Zie [Groottevoor virtuele Windows-machines in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) voor Virtuele VM's en [Formaten voor virtuele Linux-machines in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) voor Vm's voor Virtuele VM's voor Virtuele VM's.

### <a name="azure-netapp-files-volume-sizing"></a>Grootte van het azure NetApp-bestanden-volume

Zorg ervoor dat u het juiste serviceniveau en de grootte van het volumequotum kiest voor het verwachte prestatieniveau. Zie [Serviceniveaus voor Azure NetApp-bestanden](azure-netapp-files-service-levels.md) voor meer informatie.

### <a name="virtual-network-vnet-recommendations"></a>Aanbevelingen voor virtueel netwerk (VNet)

U moet de benchmarktests uitvoeren in hetzelfde VNet als Azure NetApp-bestanden. Het onderstaande voorbeeld toont de aanbeveling aan:

![VNet-aanbevelingen](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Installatie van FIO

FIO is beschikbaar in binaire indeling voor zowel Linux als Windows. Volg de sectie Binaire pakketten in [FIO](https://github.com/axboe/fio) om te installeren voor het platform van uw keuze.

## <a name="fio-examples-for-iops"></a>FIO-voorbeelden voor IOPS 

De FIO-voorbeelden in deze sectie gebruiken de volgende instelling:
* VM-instantiegrootte: D32s_v3
* Capaciteit pool service niveau en grootte: Premium / 50 TiB
* Volumequotumgrootte: 48 TiB

De volgende voorbeelden tonen de FIO random leest en schrijft.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8k blok grootte 100% random leest

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Uitvoer: 68k lees IOPS weergegeven

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8k blok grootte 100% random schrijft

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Uitvoer: 73k schrijf IOPS weergegeven

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>FIO-voorbeelden voor bandbreedte

De voorbeelden in deze sectie tonen de FIO sequentiële leest en schrijft.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64k blokgrootte 100% sequentiële leest

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Uitvoer: 11,8 Gbit/s-doorvoer weergegeven

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64k blokgrootte 100% sequentiële schrijfbewerkingen

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Uitvoer: 12,2 Gbit/s-doorvoer weergegeven

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Volumestatistieken

Prestatiegegevens van Azure NetApp Files zijn beschikbaar via Azure Monitor-tellers. De tellers zijn beschikbaar via de Azure-portal- en REST API GET-aanvragen. 

U historische gegevens bekijken voor de volgende informatie:
* Gemiddelde leeslatentie 
* Gemiddelde schrijflatentie 
* IOPS lezen (gemiddeld)
* IOPS schrijven (gemiddeld)
* Volumelogische grootte (gemiddeld)
* Grootte van volumemomentopnamen (gemiddeld)

### <a name="using-azure-monitor"></a>Azure Monitor gebruiken 

U per volume toegang krijgen tot Azure NetApp-bestandentellers op de pagina Statistieken, zoals hieronder wordt weergegeven:

![Azure Monitor-statistieken](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

U ook een dashboard maken in Azure Monitor voor Azure NetApp-bestanden door naar de pagina Statistieken te gaan, te filteren op NetApp en de volumetellers van belang op te geven: 

![Azure Monitor-dashboard](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure Monitor API-toegang

U hebt toegang tot Azure NetApp Files-tellers met REST API-aanroepen. Zie [Ondersteunde statistieken met Azure Monitor: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) voor tellers voor capaciteitspools en volumes.

In het volgende voorbeeld wordt een URL OPHALEN weergegeven voor het weergeven van logische volumegrootte:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Volgende stappen

- [Serviceniveau's voor Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Prestatiebenchmarks voor Azure NetApp Files](azure-netapp-files-performance-benchmarks.md)