---
title: Azure Linux VM-formaten - vorige generaties | Microsoft Documenten
description: Geeft een overzicht van de vorige generaties formaten die beschikbaar zijn voor virtuele Linux-machines in Azure. Bevat informatie over het aantal vCPU's, gegevensschijven en NIC's, evenals opslagdoorvoer en netwerkbandbreedte voor formaten in deze serie.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: jonbeck
ms.openlocfilehash: 7f13ab3cd6ff765bc3b1bee8e8fad7e7273f6c7d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673916"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>Vorige generaties van virtuele machinematen

Deze sectie geeft informatie over eerdere generaties van virtuele machineformaten. Deze maten kunnen nog steeds worden gebruikt, maar er zijn nieuwere generaties beschikbaar.

## <a name="f-series"></a>F-serie

De F-serie is gebaseerd op de 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell)-processor, die met Intel Turbo Boost Technology 2.0 een kloksnelheid kan halen van 3,1 GHz. Dit zijn dezelfde CPU-prestaties als de virtuele machines uit de Dv2-serie.  

Virtuele machines uit de F-serie vormen een uitstekende keuze voor workloads die snellere CPU's nodig hebben, maar niet zo veel geheugen of tijdelijke opslag per vCPU.  Voor workloads voor analysen, gameservers, webservers en batchverwerking is de F-serie wellicht een betere keuze.

ACU: 210 - 250

Premium opslag: niet ondersteund

Premium Storage caching: niet ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale temp opslagdoorvoer: IOPS/Read MBps/Write MBps | Maximale gegevensschijven/doorvoer: IOPS | Max NIC's/Verwachte netwerkbandbreedte (Mbps) |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2  | 16  | 3000/46/23    | 4/4x500   | 2/750   |
| Standard_F2  | 2  | 4  | 32  | 6000/93/46    | 8/8x500   | 2/1500  |
| Standard_F4  | 4  | 8  | 64  | 12000/187/93  | 16/16x500 | 4/3000  |
| Standard_F8  | 8  | 16 | 128 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standard_F16 | 16 | 32 | 256 | 48000/750/375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>Fs-serie <sup>1</sup>

De Fs-serie biedt alle voordelen van de F-serie, plus Premium-opslag.

ACU: 210 - 250

Premium opslag: ondersteund

Premium Storage-cache: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer in cache en tijdelijke opslag: IOPS/MBps (cachegrootte in GiB) | Maximale schijfdoorvoer zonder cache: IOPS/MBps | Max NIC's/Verwachte netwerkbandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)    | 3200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)    | 6400/96   | 2/1500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)  | 12800/192 | 4/3000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)  | 25600/384 | 8/6000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64000/512 (192) | 51200/768 | 8/12000 |

MBps = 10^6 bytes per seconde en GiB = 1024^3 bytes.

<sup>1</sup> De maximale schijfdoorvoer (IOPS of MBps) mogelijk met een Fs-serie VM kan worden beperkt door het aantal, de grootte en de striping van de aangesloten schijf(en).  Zie ontwerpen voor hoge prestaties voor [Windows](windows/premium-storage-performance.md) of [Linux](linux/premium-storage-performance.md)voor meer informatie.  


## <a name="nvv2-series"></a>NVv2-serie

**Nieuwere grootte aanbeveling**: [NVv3-serie](nvv3-series.md)

De Virtuele Machines uit de NVv2-serie worden aangedreven door [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU's en NVIDIA GRID-technologie met Intel Broadwell-cpu's. Deze virtuele machines zijn gericht op GPU-versnelde grafische toepassingen en virtuele desktops waar klanten hun gegevens willen visualiseren, resultaten willen simuleren om te bekijken, aan CAD willen werken of inhoud willen renderen en streamen. Bovendien kunnen deze virtuele machines enkele precisieworkloads uitvoeren, zoals codering en rendering. NVv2 virtuele machines ondersteunen Premium Storage en worden geleverd met twee keer het systeemgeheugen (RAM) in vergelijking met zijn voorganger NV-serie.  

Elke GPU in NVv2-exemplaren wordt geleverd met een GRID-licentie. Deze licentie geeft u de flexibiliteit om een NV-exemplaar te gebruiken als een virtueel werkstation voor één gebruiker, of 25 gelijktijdige gebruikers kunnen verbinding maken met de VM voor een virtueel toepassingsscenario.

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Max. aantal NIC's | Virtuele werkstations | Virtuele toepassingen |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="older-generations-of-virtual-machine-sizes"></a>Oudere generaties van virtuele machinematen

Deze sectie geeft informatie over oudere generaties van virtuele machineformaten. Deze maten worden nog steeds ondersteund, maar ontvangen geen extra capaciteit. Er zijn nieuwere of alternatieve maten die algemeen beschikbaar zijn. Raadpleeg [formaten voor virtuele Linux-machines in Azure](linux/sizes.md) om de VM-formaten te kiezen die het beste bij uw behoefte passen.  

Zie Het formaat van een Linux-vm wijzigen voor meer informatie over het wijzigen van het formaat van [een Linux-vm.](linux/change-vm-size.md)  

<br>

### <a name="basic-a"></a>Basic A  

**Nieuwere grootte aanbeveling**: [Av2-serie](av2-series.md)

Premium opslag: niet ondersteund

Premium Storage caching: niet ondersteund

De basislaaggrootten zijn met name bedoeld voor ontwikkelworkloads en andere toepassingen waarvoor geen taakverdeling, automatische schaling of geheugenintensieve virtuele machines zijn vereist.

| Grootte - grootte\naam | vCPU | Geheugen|NIC's (max.)| Maximumgrootte van tijdelijke schijf | Met maximaal gegevensschijven (elk 1023 GB)| Met maximaal IOPS (300 per schijf) |
|---|---|---|---|---|---|---|
| A0\Basic_A0 | 1 | 768 MB  | 2 | 20 GB  | 1  | 1x300  |
| A1\Basic_A1 | 1 | 1,75 GB | 2 | 40 GB  | 2  | 2x300  |
| A2\Basic_A2 | 2 | 3,5 GB  | 2 | 60 GB  | 4  | 4x300  |
| A3\Basic_A3 | 4 | 7 GB    | 2 | 120 GB | 8  | 8x300  |
| A4\Basic_A4 | 8 | 14 GB   | 2 | 240 GB | 16 | 16x300 |

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 - A4 met CLI en PowerShell

In het klassieke implementatiemodel verschillen sommige namen van VM-grootten enigszins in CLI en in PowerShell:

* Standard_A0 is ExtraSmall
* Standard_A1 is Small
* Standard_A2 is Medium
* Standard_A3 is Large
* Standard_A4 is ExtraLarge

### <a name="a-series"></a>A-serie  

**Nieuwere grootte aanbeveling**: [Av2-serie](av2-series.md)

ACU: 50-100

Premium opslag: niet ondersteund

Premium Storage caching: niet ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (HDD): GiB | Max. aantal gegevensschijven | Max. doorvoer gegevensschijf: IOPS | Max NIC's/Verwachte netwerkbandbreedte (Mbps) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1.</sup> | 1 | 0,768 | 20 | 1 | 1 x 500 | 2/100 |
| Standard_A1 | 1 | 1,75 | 70  | 2  | 2 x 500  | 2/500  |
| Standard_A2 | 2 | 3,5  | 135 | 4  | 4 x 500  | 2/500  |
| Standard_A3 | 4 | 7    | 285 | 8  | 8 x 500  | 2/1000 |
| Standard_A4 | 8 | 14   | 605 | 16 | 16 x 500 | 4/2000 |
| Standard_A5 | 2 | 14   | 135 | 4  | 4 x 500  | 2/500  |
| Standard_A6 | 4 | 28   | 285 | 8  | 8 x 500  | 2/1000 |
| Standard_A7 | 8 | 56   | 605 | 16 | 16 x 500 | 4/2000 |

<sup>1</sup> De A0-maat is over-geabonneerd op de fysieke hardware. Alleen bij deze specifieke grootte kunnen implementaties van andere klanten invloed hebben op de prestaties van uw uitgevoerde workload. De relatieve prestaties worden hieronder beschreven, zoals de verwachte basislijn, met een variabiliteit van ongeveer 15 procent.

<br>

### <a name="a-series---compute-intensive-instances"></a>A-serie: rekenintensieve exemplaren  

**Nieuwere grootte aanbeveling**: [Av2-serie](av2-series.md)

ACU: 225

Premium opslag: niet ondersteund

Premium Storage caching: niet ondersteund

Grootten uit de A8-A11- en H-serie worden ook wel *rekenintensieve exemplaren* genoemd. De hardware waarop deze grootten worden uitgevoerd, is ontworpen en geoptimaliseerd voor rekenintensieve en netwerkintensieve toepassingen, waaronder HPC-clustertoepassingen (high-performance computing), modellerings- en simulatietoepassingen. De A8-A11-serie gebruikt Intel Xeon E5-2670 @ 2,6 GHZ en de H-serie gebruikt Intel Xeon E5-2667 v3 @ 3,2 GHz.  

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (HDD): GiB | Max. aantal gegevensschijven | Max. doorvoer gegevensschijf: IOPS | Max. aantal NIC's|
|---|---|---|---|---|---|---|
| Standard_A8&nbsp;<sup>1.</sup> | 8  | 56  | 382 | 32 | 32 x 500 | 2 |
| Standard_A9&nbsp;<sup>1.</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32 x 500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1.</sup> Voor MPI-toepassingen wordt het speciale RDMA-backend-netwerk ingeschakeld door het FDR InfiniBand-netwerk, dat ultralage latentie en hoge bandbreedte levert.  

> [!NOTE]
> De A8 – A11 VM's zijn gepland voor pensionering op 3/2021. Zie [HPC-migratiegids](https://azure.microsoft.com/resources/hpc-migration-guide/)voor meer informatie.

<br>

### <a name="d-series"></a>D-serie  

**Nieuwere grootte aanbeveling**: [Dv3-serie](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Premium opslag: niet ondersteund

Premium Storage caching: niet ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale temp opslagdoorvoer: IOPS/Read MBps/Write MBps | Maximale gegevensschijven/doorvoer: IOPS | Max NIC's/Verwachte netwerkbandbreedte (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D1  | 1 | 3,5 | 50  | 3000/46/23    | 4/4x500   | 2/500  |
| Standard_D2  | 2 | 7   | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D3  | 4 | 14  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D4  | 8 | 28  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |

<sup>1</sup> VM-familie kan draaien op een van de volgende CPU's: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) of 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>D-serie - geheugen geoptimaliseerd  

**Nieuwere grootte aanbeveling**: [Dv3-serie](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Premium opslag: niet ondersteund

Premium Storage caching: niet ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale temp opslagdoorvoer: IOPS/Read MBps/Write MBps | Maximale gegevensschijven/doorvoer: IOPS | Max NIC's/Verwachte netwerkbandbreedte (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D11 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D12 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D13 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |
| Standard_D14 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/8000 |

<sup>1</sup> VM-familie kan draaien op een van de volgende CPU's: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) of 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

## <a name="preview-dc-series"></a>Preview: DC-serie

Premium opslag: ondersteund

Premium Storage-cache: ondersteund

De DC-serie maakt gebruik van de nieuwste generatie van 3,7 GHz Intel XEON E-2176G Processor met SGX-technologie, en met de Intel Turbo Boost-technologie kan oplopen tot 4,7 GHz. 

| Grootte          | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Max NIC's / Verwachte netwerkbandbreedte (Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |

> [!IMPORTANT]
>
> VM's uit de DC-serie zijn `Gen2` generatie 2 [VM's](./linux/generation-2.md#creating-a-generation-2-vm) en ondersteunen alleen afbeeldingen.


### <a name="ds-series"></a>DS-serie  

**Nieuwere grootte aanbeveling**: [Dsv3-serie](dv3-dsv3-series.md)

ACU: 160-250 <sup>1</sup>

Premium opslag: ondersteund

Premium Storage-cache: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer in cache en tijdelijke opslag: IOPS/MBps (cachegrootte in GiB) | Maximale schijfdoorvoer zonder cache: IOPS/MBps | Max NIC's/Verwachte netwerkbandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3,5 | 7  | 4  | 4000/32 (43)    | 3200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)    | 6400/64   | 2/1000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172) | 12800/128 | 4/2000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344) | 25600/256 | 8/4000 |

<sup>1</sup> VM-familie kan draaien op een van de volgende CPU's: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) of 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS-serie - geheugen geoptimaliseerd  

**Nieuwere grootte aanbeveling**: [Dsv3-serie](dv3-dsv3-series.md)

ACU: 160-250 <sup>1,2</sup>

Premium opslag: ondersteund

Premium Storage-cache: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer in cache en tijdelijke opslag: IOPS/MBps (cachegrootte in GiB) | Maximale schijfdoorvoer zonder cache: IOPS/MBps | Max NIC's/Verwachte netwerkbandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/64   | 2/1000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/128 | 4/2000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/256 | 8/4000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/512 | 8/8000 |

<sup>1</sup> De maximale schijfdoorvoer (IOPS of MBps) mogelijk met een DS-serie VM kan worden beperkt door het aantal, de grootte en de striping van de aangesloten schijf(en).  Zie ontwerpen voor hoge prestaties voor [Windows](windows/premium-storage-performance.md) of [Linux](linux/premium-storage-performance.md)voor meer informatie.
<sup>2</sup> VM-familie kan draaien op een van de volgende CPU's: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) of 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Ls-serie

De Ls-serie biedt maximaal 32 vCPU's en maakt gebruik van een [Intel Xeon®-processor uit de E5 v3-familie](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). De Ls-serie heeft dezelfde CPU-prestaties als de G/GS-serie en wordt geleverd met 8 GiB geheugen per vCPU.

De Ls-serie ondersteunt niet de creatie van een lokale cache om de IOPS haalbaar te maken door duurzame dataschijven. De hoge doorvoer en IOPS van de lokale schijf maakt Ls-serie VM's ideaal voor NoSQL-winkels zoals Apache Cassandra en MongoDB die gegevens repliceren over meerdere VM's om persistentie te bereiken in het geval van het falen van een enkele VM.

ACU: 180-240

Premium opslag: ondersteund

Premium Storage caching: niet ondersteund

| Grootte | vCPU | Geheugen (GiB) | Tijdelijke opslag (GiB) | Max. aantal gegevensschijven | Maximale temp-opslagdoorvoer (IOPS/MBps) | Maximale schijfdoorvoer zonder cache (IOPS/MBps) | Max NIC's/Verwachte netwerkbandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| Standard_L32s&nbsp;<sup>1.</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

De maximale schijfdoorvoer die mogelijk is met VM's uit de Ls-serie kan worden beperkt door het aantal, de grootte en de striping van aangesloten schijven. Zie ontwerpen voor hoge prestaties voor [Windows](windows/premium-storage-performance.md) of [Linux](linux/premium-storage-performance.md)voor meer informatie.

<sup>1</sup> Instantie is geïsoleerd voor hardware die is gewijd aan één klant.

### <a name="gs-series"></a>GS-serie

ACU: 180 - 240 <sup>1</sup>

Premium opslag: ondersteund

Premium Storage-cache: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Maximale schijfdoorvoer zonder cache: IOPS/MBps | Max NIC's/Verwachte netwerkbandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)  | 5000/ 125  | 2/2000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)  | 10000/ 250 | 2/4000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056) | 20000/ 500 | 4/8000 |
| Standard_GS4&nbsp;<sup>3.</sup> | 16 | 224 | 448 | 64 | 80000/800 (2112) | 40000/1000 | 8/16000 |
| Standard_GS5&nbsp;<sup>2.&nbsp;</sup> | 32 | 448 |896 | 64 |160000/1600 (4224) | 80000/2000 | 8/20000 |

<sup>1</sup> De maximale schijfdoorvoer (IOPS of MBps) mogelijk met een GS-serie VM kan worden beperkt door het aantal, de grootte en de striping van de aangesloten schijf(en). Zie ontwerpen voor hoge prestaties voor [Windows](windows/premium-storage-performance.md) of [Linux](linux/premium-storage-performance.md)voor meer informatie.

<sup>2</sup> Instance is geïsoleerd voor hardware die is gewijd aan één klant.

<sup>3</sup> Beperkte kerngroottes beschikbaar.

<br>

### <a name="g-series"></a>G-serie

ACU: 180 - 240

Premium opslag: niet ondersteund

Premium Storage caching: niet ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale temp opslagdoorvoer: IOPS/Read MBps/Write MBps | Maximale gegevensschijven/doorvoer: IOPS | Max NIC's/Verwachte netwerkbandbreedte (Mbps) |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6000/93/46    | 8/8x500   | 2/2000  |
| Standard_G2  | 4  | 56  | 768  | 12000/187/93  | 16/16x500 | 2/4000  |
| Standard_G3  | 8  | 112 | 1536 | 24000/375/187 | 32/32x500 | 4/8000  |
| Standard_G4  | 16 | 224 | 3072 | 48000/750/375 | 64/64x500 | 8/16000 |
| Standard_G5&nbsp;<sup>1.</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> Instantie is geïsoleerd voor hardware die is gewijd aan één klant.
<br>

# <a name="nv-series"></a>NV-serie
**Nieuwere grootte aanbeveling**: [NVv3-serie](nvv3-series.md) en [NVv4-serie](nvv4-series.md)

De virtuele machines uit de NV-serie worden aangedreven door [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU's en NVIDIA GRID-technologie voor desktopversnelde toepassingen en virtuele desktops waar klanten hun gegevens of simulaties kunnen visualiseren. Gebruikers kunnen hun grafisch intensieve workflows op de NV-instanties visualiseren om superieure grafische mogelijkheden te krijgen en bovendien enkele precisieworkloads uit te voeren, zoals codering en rendering. NV-serie VM's worden ook aangedreven door Intel Xeon E5-2690 v3 (Haswell) CPU's.

Elke GPU in NV-exemplaren wordt geleverd met een GRID-licentie. Deze licentie geeft u de flexibiliteit om een NV-exemplaar te gebruiken als een virtueel werkstation voor één gebruiker, of 25 gelijktijdige gebruikers kunnen verbinding maken met de VM voor een virtueel toepassingsscenario.

Premium opslag: niet ondersteund

Premium Storage caching: niet ondersteund

Live migratie: niet ondersteund

Updates voor geheugenbehoud: niet ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Max. aantal NIC's | Virtuele werkstations | Virtuele toepassingen |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = halve M60-kaart.
<br>

## <a name="other-sizes"></a>Andere maten

* [Algemeen doel](sizes-general.md)
* [Geoptimaliseerde rekenkracht](sizes-compute.md)
* [Geoptimaliseerd geheugen](sizes-memory.md)
* [Geoptimaliseerde opslag](sizes-storage.md)
* [GPU](sizes-gpu.md)
* [Krachtig rekenvermogen](sizes-hpc.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure compute units (ACU)](acu.md) u kunnen helpen bij het vergelijken van rekenprestaties in Azure SKU's.
