---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 08/15/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 1867164954a3f9dff7a8a8c04e249a13edccb84a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020895"
---
Deze sectie bevat informatie over oudere generaties virtuele machine grootten. Deze grootten worden nog steeds ondersteund, maar er is geen aanvullende capaciteit. Er zijn nieuwe nieuwere of alternatieve grootten die algemeen beschikbaar zijn. Zie [grootten voor virtuele Windows-machines in azure](../articles/virtual-machines/windows/sizes.md) of [grootten voor virtuele Linux-machines in azure](../articles/virtual-machines/linux/sizes.md) om te kiezen welke VM-grootten het meest geschikt is voor uw behoeften.  

Zie [het formaat van een virtuele Linux-machine wijzigen met Azure cli](../articles/virtual-machines/linux/change-vm-size.md)voor meer informatie over het wijzigen van het formaat van een Linux-VM. Zie [het formaat van een Windows-VM wijzigen](../articles/virtual-machines/windows/resize-vm.md)als u Windows-vm's gebruikt en de voor keur geeft aan Power shell.  

<br>

### <a name="basic-a"></a>Basic A  

**Aanbeveling voor nieuwere grootte**: [Av2-serie](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

De basislaaggrootten zijn met name bedoeld voor ontwikkelworkloads en andere toepassingen waarvoor geen taakverdeling, automatische schaling of geheugenintensieve virtuele machines zijn vereist.

|Grootte - grootte\naam | vCPU |Geheugen|NIC's (max.)|Maximumgrootte van tijdelijke schijf |Met maximaal gegevens schijven (1023 GB elk)|Met maximaal IOPS (300 per schijf)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1x300|
|A1\Basic_A1|1|1,75 GB|2| 40 GB |2|2x300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4x300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8x300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16x300|

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 - A4 met CLI en PowerShell

In het klassieke implementatiemodel verschillen sommige namen van VM-grootten enigszins in CLI en in PowerShell:

* Standard_A0 is ExtraSmall
* Standard_A1 is Small
* Standard_A2 is Medium
* Standard_A3 is Large
* Standard_A4 is ExtraLarge

### <a name="a-series"></a>A-serie  

**Aanbeveling voor nieuwere grootte**: [Av2-serie](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 50-100

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (HDD): GiB | Maximaal aantal gegevensschijven | Max. doorvoer gegevensschijf: IOPS | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0,768 |20 |1 |1 x 500 |2 / 100 |
| Standard_A1 |1 |1,75 |70 |2 |2 x 500 |2 / 500  |
| Standard_A2 |2 |3,5 |135 |4 |4 x 500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8 x 500 |2 / 1000 |
| Standard_A4 |8 |14 |605 |16 |16 x 500 |4 / 2000 |
| Standard_A5 |2 |14 |135 |4 |4 x 500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8 x 500 |2 / 1000 |
| Standard_A7 |8 |56 |605 |16 |16 x 500 |4 / 2000 |

<sup>1</sup> de a0-grootte is te veel geabonneerd op de fysieke hardware. Alleen bij deze specifieke grootte kunnen implementaties van andere klanten invloed hebben op de prestaties van uw uitgevoerde workload. De relatieve prestaties worden hieronder beschreven, zoals de verwachte basislijn, met een variabiliteit van ongeveer 15 procent.

<br>

### <a name="a-series---compute-intensive-instances"></a>A-serie: rekenintensieve exemplaren  

**Aanbeveling voor nieuwere grootte**: [Av2-serie](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 225

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

Grootten uit de A8-A11- en H-serie worden ook wel *rekenintensieve exemplaren* genoemd. De hardware waarop deze grootten worden uitgevoerd, is ontworpen en geoptimaliseerd voor rekenintensieve en netwerkintensieve toepassingen, waaronder HPC-clustertoepassingen (high-performance computing), modellerings- en simulatietoepassingen. De A8-A11-serie gebruikt Intel Xeon E5-2670 @ 2,6 GHZ en de H-serie gebruikt Intel Xeon E5-2667 v3 @ 3,2 GHz.  

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (HDD): GiB | Maximaal aantal gegevensschijven | Max. doorvoer gegevensschijf: IOPS | Maximale aantal NIC's|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32 x 500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32 x 500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup> Voor MPI-toepassingen wordt een toegewijd RDMA-back-end-netwerk ingeschakeld door QDR InfiniBand Network, dat zeer lage latentie en hoge band breedte levert.  

<br>

### <a name="d-series"></a>D-reeks  

**Aanbeveling voor nieuwere grootte**: [Dv3-serie](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

| Grootte         | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale tijdelijke opslagdoorvoer: IOPS / MBps lezen / MBps schrijven | Maximumaantal gegevensschijven / doorvoer: IOPS | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3,5         | 50             | 3000 / 46 / 23                                           | 4 / 4 x 500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12.000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 2000                     |
| Standard_D4  | 8         | 28          | 400            | 24.000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 4000                     |

<sup>1</sup> VM-familie kan worden uitgevoerd op een van de volgende cpu's: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 v3 (Haswell) of 2,3 GHz intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>D-Series-geoptimaliseerd voor geheugen  

**Aanbeveling voor nieuwere grootte**: [Dv3-serie](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

| Grootte         | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale tijdelijke opslagdoorvoer: IOPS / MBps lezen / MBps schrijven | Maximumaantal gegevensschijven / doorvoer: IOPS | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12.000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24.000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48.000 / 750 / 375                                        | 64/64x500                       | 8 / 8000                |

<sup>1</sup> VM-familie kan worden uitgevoerd op een van de volgende cpu's: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 v3 (Haswell) of 2,3 GHz intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series"></a>DS-serie  

**Aanbeveling voor nieuwere grootte**: [DSv3-serie](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1</sup>

Premium Storage: ondersteund

Premium Storage caching: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximaal aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3,5 |7 |4 |4000 / 32 (43) |3200 / 32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8000 / 64 (86) |6400 / 64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16.000 / 128 (172) |12.800 / 128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32.000 / 256 (344) |25.600 / 256 |8 / 4000 |

<sup>1</sup> VM-familie kan worden uitgevoerd op een van de volgende cpu's: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 v3 (Haswell) of 2,3 GHz intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS-Series-geoptimaliseerd voor geheugen  

**Aanbeveling voor nieuwere grootte**: [DSv3-serie](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1, 2</sup>

Premium Storage: ondersteund

Premium Storage caching: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximaal aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8000 / 64 (72) |6400 / 64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16.000 / 128 (144) |12.800 / 128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32.000 / 256 (288) |25.600 / 256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64.000 / 512 (576) |51.200 / 512 |8 / 8000 |

<sup>1</sup> de maximale schijf doorvoer (IOPS of Mbps) die mogelijk is met een VM van de DS-serie kan worden beperkt door het aantal, de grootte en de striping van de gekoppelde schijven.  Zie [ontwerpen voor hoge prestaties](../articles/virtual-machines/windows/premium-storage-performance.md)voor meer informatie.   
<sup>2</sup> VM-familie kan worden uitgevoerd op een van de volgende cpu's: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 v3 (Haswell) of 2,3 GHz intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Ls-serie

De Ls-serie biedt maximaal 32 vCPU's en maakt gebruik van een [Intel Xeon®-processor uit de E5 v3-familie](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). De Ls-serie heeft dezelfde CPU-prestaties als de G/GS-serie en wordt geleverd met 8 GiB geheugen per vCPU.

De LS-serie biedt geen ondersteuning voor het maken van een lokale cache om het aantal IOPS dat door duurzame gegevens schijven kan worden behaald, te verg Roten. Dankzij de hoge door Voer en IOPS van de lokale schijf zijn de Vm's uit de LS-serie ideaal voor NoSQL-archieven zoals Apache Cassandra en MongoDB die gegevens repliceren op meerdere Vm's om persistentie te krijgen in het geval van een storing in één virtuele machine.

ACU: 180-240

Premium Storage: ondersteund

Premium Storage caching: niet ondersteund
 
| Grootte          | vCPU | Geheugen (GiB) | Tijdelijke opslag (GiB) | Maximaal aantal gegevensschijven | Maximale tijdelijke opslag doorvoer (IOPS/MBps) | Maxi maal aantal niet-opgeslagen schijf doorvoer (IOPS/MBps) | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20000/200 | 5000/125  | 2 / 4000  | 
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4 / 8000  | 
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8 / 16000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5630 | 64   | 160000/1600   | 40000/1000     | 8 / 20000 | 

De maximale schijf doorvoer die mogelijk is met Vm's uit de LS-serie kan worden beperkt door het aantal, de grootte en de striping van de gekoppelde schijven. Zie [ontwerpen voor hoge prestaties](../articles/virtual-machines/windows/premium-storage-performance.md)voor meer informatie.

<sup>1</sup> exemplaar is geïsoleerd voor hardware, speciaal voor één klant.

### <a name="gs-series"></a>GS-serie 

ACU: 180-240 <sup>1</sup>

Premium Storage: ondersteund

Premium Storage caching: ondersteund

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximaal aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10.000 / 100 (264) |5000 / 125 |2 / 2000 |
| Standard_GS2 |4 |56 |112 |16 |20.000 / 200 (528) |10.000 / 250 |2 / 4000 |
| Standard_GS3 |8 |112 |224 |32 |40.000 / 400 (1,056) |20.000 / 500 |4 / 8000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80.000 / 800 (2,112) |40.000 / 1,000 |8 / 16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160.000 / 1600 (4,224) |80.000 / 2000 |8 / 20000 |

<sup>1</sup> de maximale schijf doorvoer (IOPS of Mbps) die mogelijk is met een GS-serie VM kan worden beperkt door het aantal, de grootte en de striping van de gekoppelde schijven. Zie [ontwerpen voor hoge prestaties](../articles/virtual-machines/windows/premium-storage-performance.md)voor meer informatie.

<sup>2</sup> exemplaar is geïsoleerd voor hardware, speciaal voor één klant.

<sup>3</sup> beperkte core-grootten beschikbaar.

<br>

### <a name="g-series"></a>G-reeks

ACU: 180 - 240

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

| Grootte         | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale tijdelijke opslagdoorvoer: IOPS / MBps lezen / MBps schrijven | Maximumaantal gegevensschijven / doorvoer: IOPS | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8 x 500                       | 2 / 2000                     |
| Standard_G2  | 4         | 56          | 768            | 12.000 / 187 / 93                                         | 16 / 16 x 500                       | 2 / 4000                     |
| Standard_G3  | 8         | 112         | 1536          | 24.000 / 375 / 187                                        | 32 / 32 x 500                     | 4 / 8000                |
| Standard_G4  | 16        | 224         | 3072          | 48.000 / 750 / 375                                        | 64 / 64 x 500                     | 8 / 16000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6144          | 96.000 / 1500 / 750                                       | 64 / 64 x 500                     | 8 / 20000           |

<sup>1</sup> exemplaar is geïsoleerd voor hardware, speciaal voor één klant.
<br>
