---
title: Lsv2-serie - Azure Virtual Machines
description: Specificaties voor de Lsv2-serie VM's.
services: virtual-machines
author: sasha-melamed
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 103e19d6e299956b5ee1ad45b577e25f9f2de1c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164029"
---
# <a name="lsv2-series"></a>Lsv2-serie

De Lsv2-serie beschikt over een hoge doorvoer, lage latentie, direct in kaart gebrachtlokale NVMe-opslag draait op de [AMD EPYC<sup>TM</sup> 7551 processor](https://www.amd.com/en/products/epyc-7000-series) met een all core boost van 2,55 GHz en een maximale boost van 3,0 GHz. De Lsv2-serie VM's zijn er in maten van 8 tot 80 vCPU in een gelijktijdige multi-threading configuratie.  Er is 8 GiB geheugen per vCPU, en een 1,92 TB NVMe SSD M.2-apparaat per 8 vCPU's, met maximaal 19,2 TB (10x1,92TB) beschikbaar op de L80s v2.

> [!NOTE]
> De VM's uit de Lsv2-serie zijn geoptimaliseerd om de lokale schijf op het knooppunt dat rechtstreeks aan de VM is gekoppeld, te gebruiken in plaats van duurzame gegevensschijven te gebruiken. Dit zorgt voor een grotere IoPs / doorvoer voor uw workloads. De Lsv2- en Ls-serie ondersteunen geen lokale cache om de ISP's te verhogen die haalbaar zijn voor duurzame gegevensschijven.
>
> De hoge doorvoer en IPP's van de lokale schijf maakt de Lsv2-serie VM's ideaal voor NoSQL-winkels zoals Apache Cassandra en MongoDB die gegevens repliceren over meerdere VM's om persistentie te bereiken in het geval van het falen van een enkele VM.
>
> Zie Prestaties optimaliseren op de virtuele machines uit de Lsv2-serie voor [Windows](../virtual-machines/windows/storage-performance.md) of [Linux](../virtual-machines/linux/storage-performance.md)voor meer informatie.  

ACU: 150-175

Premium opslag: ondersteund

Premium Storage caching: niet ondersteund

Live migratie: niet ondersteund

Updates voor geheugenbehoud: niet ondersteund

| Grootte | vCPU | Geheugen (GiB) | Temp schijf<sup>1</sup> (GiB) | NVMe-schijven<sup>2</sup> | NVMe-schijfdoorvoer<sup>3</sup> (Lees IOPS/MBps) | Maximale gegevensdoorvoer zonder cache (IBP's/MBps)<sup>4</sup> | Maximale gegevensschijven | Max NIC's / Verwachte netwerkbandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1,92 TB  | 400000/2000  | 8000/160   | 16 | 2 / 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1,92 TB  | 800000/4000  | 16000/320  | 32 | 4 / 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1,92 TB  | 1,5 M/8000    | 32000/640  | 32 | 8 / 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x1,92 TB  | 2.2M/14000   | 48000/960  | 32 | 8 / 16000+ |
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1,92 TB  | 2.9M/16000   | 64000/1280 | 32 | 8 / 16000+ |
| Standard_L80s_v2<sup>5.</sup> | 80 | 640 | 800 | 10x1,92 TB | 3,8 M/20000 | 80000/1400 | 32 | 8 / 16000+ |

<sup>1</sup> Lsv2-serie VM's hebben een standaard SCSI-gebaseerde temp resource disk voor OS paging / swap bestandsgebruik (D: op Windows, / dev / sdb op Linux). Deze schijf biedt 80 GiB-opslag, 4.000 IOPS en 80 MBps-overdrachtssnelheid voor elke 8 vCPU's (bijvoorbeeld Standard_L80s_v2 800 GiB biedt bij 40.000 IOPS en 800 MBPS). Dit zorgt ervoor dat de NVMe-schijven volledig kunnen worden besteed aan toepassingsgebruik. Deze schijf is vluchtig en alle gegevens gaan verloren bij het stoppen/detoewijzen.

<sup>2</sup> Lokale NVMe-schijven zijn vluchtig, gegevens gaan verloren op deze schijven als u uw VM stopt/deallocateeert.

<sup>3</sup> Hyper-V NVMe Direct-technologie biedt onthrottled toegang tot lokale NVMe-schijven die veilig in de VM-ruimte van de gast zijn in kaart gebracht.  Om maximale prestaties te bereiken, moet u de nieuwste WS2019-build of Ubuntu 18.04 of 16.04 van de Azure Marketplace gebruiken.  Schrijfprestaties variëren afhankelijk van io-grootte, schijfbelasting en capaciteitsbenutting.

<sup>Vm's</sup> uit de 4 Lsv2-serie bieden geen hostcache voor gegevensschijf, omdat deze niet ten goede komt aan de Lsv2-workloads.  Lsv2 VM's zijn echter geschikt voor de epttale VM OS-schijfoptie van Azure (tot 30 GiB).

<sup>5</sup> VM's met meer dan 64 vCPU's vereisen een van deze ondersteunde gastbesturingssystemen:

- Windows Server 2016 of hoger
- Ubuntu 16.04 LTS of hoger, met Azure tuned kernel (4.15 kernel of hoger)
- SLES 12 SP2 of hoger
- RHEL- of CentOS-versie 6.7 tot en met 6.10, met door Microsoft geleverd LIS-pakket 4.3.1 (of hoger) geïnstalleerd
- RHEL- of CentOS-versie 7.3, met door Microsoft geleverd LIS-pakket 4.2.1 (of hoger) geïnstalleerd
- RHEL- of CentOS-versie 7.6 of hoger
- Oracle Linux met UEK4 of hoger
- Debian 9 met de backports kernel, Debian 10 of hoger
- CoreOS met een 4.14 kernel of hoger

## <a name="size-table-definitions"></a>De grootte van tabeldefinities wijzigen

- De opslagcapaciteit wordt weergegeven in GiB-eenheden of 1024^3 bytes. Wanneer u schijven die zijn gemeten in GB (1000^3 bytes), vergelijkt met schijven die zijn gemeten in GiB (1024^3), moet u voor ogen houden dat de capaciteit in GiB kleiner lijkt te zijn. 1023 GiB is bijvoorbeeld gelijk aan 1098,4 GB
- De schijfdoorvoer wordt gemeten in I/O-bewerkingen per seconde (IOPS) en MBps, waarbij MBps = 10^6 bytes per seconde.
- Als u de beste prestaties voor uw VM's wilt krijgen, moet u het aantal gegevensschijven beperken tot 2 schijven per vCPU.
- **Verwachte netwerkbandbreedte** is de maximale geaggregeerde [bandbreedte die per VM-type wordt toegewezen](../virtual-network/virtual-machine-network-throughput.md) voor alle NIC's, voor alle bestemmingen. Bovengrenzen worden niet gegarandeerd, maar zijn bedoeld als richtlijn voor het selecteren van het juiste VM-type voor de bedoelde toepassing. De werkelijke netwerkprestaties zijn afhankelijk van verschillende factoren, waaronder netwerkcongestie, toepassingsbelastingen en netwerkinstellingen. Zie [Netwerkdoorvoer optimaliseren voor Windows en Linux](../virtual-network/virtual-network-optimize-network-bandwidth.md) voor meer informatie over het optimaliseren van de netwerkdoorvoer. Om de verwachte netwerkprestaties op Linux en Windows te bereiken, kan het noodzakelijk zijn een specifieke versie te selecteren of de VM te optimaliseren. Zie [How to reliably test for virtual machine throughput](../virtual-network/virtual-network-bandwidth-testing.md) (Betrouwbaar testen van de doorvoer van virtuele machines) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure compute units (ACU)](acu.md) u kunnen helpen bij het vergelijken van rekenprestaties in Azure SKU's.
