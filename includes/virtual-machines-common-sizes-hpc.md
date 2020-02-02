---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 29e453dd6a9ea7ac83d84adb7eb0f3d998c1eaaf
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961335"
---
Azure HPC Optimized virtual machines (Vm's) zijn ontworpen voor het leveren van prestaties van een leiderschaps klasse, MPI schaal baarheid en rendabele efficiëntie voor een groot aantal toepassingen.
 
## <a name="infiniband-networking-for-large-scale-hpc"></a>InfiniBand-netwerken voor grootschalige HPC
HBv2 Vm's feature 200 GB/sec Mellanox HDR InfiniBand, terwijl zowel de HB-als de HC-Vm's functie 100 GB/sec Mellanox EDR InfiniBand zijn. Elk van deze VM-typen is verbonden met een niet-blokkerende Fat-structuur voor geoptimaliseerde en consistente RDMA-prestaties.

HBv2 Vm's ondersteunen adaptieve route ring en het dynamisch verbonden Trans Port (DCT, in aanvulling op standaard-RC-en UD-transporten). Deze functies verbeteren de prestaties, schaal baarheid en consistentie van toepassingen, en het gebruik ervan wordt ten zeerste aanbevolen.  

HBv2-, HB-en HC-Vm's ondersteunen standaard Mellanox/OFED-Stuur Programma's. Als zodanig worden alle RDMA-werk woorden en MPI-typen ondersteund. Elk van deze VM-typen biedt ook ondersteuning voor op hardware gebaseerde offload van MPI collectief voor betere toepassings prestaties.
 
Oorspronkelijke functie Vm's van de H-serie 56 GB/sec Mellanox FDR InfiniBand. Om gebruik te maken van de InfiniBand-interface op de H-serie, moeten klanten implementeren met behulp van officieel ondersteunde installatie kopieën die specifiek zijn voor dit VM-type vanuit Azure Marketplace. 


## <a name="hbv2-series"></a>HBv2-serie
Vm's uit de HBv2-serie zijn geoptimaliseerd voor toepassingen die worden aangedreven door geheugen bandbreedte, zoals een Hydraulic-dynamiek, een eindige element analyse en reservoir simulatie. HBv2 Vm's feature 120 AMD EPYC 7742-processor kernen, 4 GB RAM per CPU-kern en zonder gelijktijdige multi threading. Elke HBv2-VM biedt tot 340 GB/sec. geheugen bandbreedte en Maxi maal 4 teraFLOPS van FP64 compute. 

Premium Storage: ondersteund

| Grootte | vCPU | Processor | Geheugen (GB) | Geheugen bandbreedte GB/s | Basis-CPU-frequentie (GHz) | Frequentie van alle kernen (GHz, piek) | Frequentie met één kern geheugen (GHz, piek) | RDMA-prestaties (GB/s) | MPI-ondersteuning | Tijdelijke opslag (GB) | Maximaal aantal gegevensschijven | Maximum aantal Ethernet Nic's |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3,3 | 200 | Alles | 480 + 960 | 8 | 1 |

<br>

## <a name="hb-series"></a>HB-serie
Virtuele machines uit de HB-serie zijn geoptimaliseerd voor toepassingen die geheugenbandbreedte gebruiken, zoals vloeiende dynamics, expliciete beperkte elementanalyse en weermodellen. HB Vm's feature 60 AMD EPYC 7551-processor kernen, 4 GB RAM per CPU-kern en zonder gelijktijdige multi threading. Een HB-VM biedt tot 260 GB/sec. geheugen bandbreedte.  

ACU: 199-216

Premium Storage: ondersteund

Premium Storage caching: ondersteund

| Grootte | vCPU | Processor | Geheugen (GiB) | Geheugen bandbreedte GiB/s | Basis-CPU-frequentie (GHz) | Frequentie van alle kernen (GHz, piek) | Frequentie met één kern geheugen (GHz, piek) | RDMA-prestaties (GiB/s) | MPI-ondersteuning | Tijdelijke opslag (GiB) | Maximaal aantal gegevensschijven | Maximum aantal Ethernet Nic's |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Alles | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>HC-serie
Vm's uit de HC-serie zijn geoptimaliseerd voor toepassingen die worden aangedreven door een dichte berekening, zoals impliciete, geeindigd element analyse, moleculaire dynamiek en reken kundige schei kunde. HC-VM’s zijn voorzien van 44 Intel Xeon Platinum 8168-processorkernen en 8 GB aan RAM-geheugen per CPU-kern, zonder hyperthreading. Het Intel Xeon Platinum-platform biedt ondersteuning voor een uitgebreid ecosysteem van Intel-software Programma's zoals een Intel math kernel-bibliotheek. 

ACU: 297-315

Premium Storage: ondersteund

Premium Storage caching: ondersteund


| Grootte | vCPU | Processor | Geheugen (GiB) | Geheugen bandbreedte GiB/s | Basis-CPU-frequentie (GHz) | Frequentie van alle kernen (GHz, piek) | Frequentie met één kern geheugen (GHz, piek) | RDMA-prestaties (GiB/s) | MPI-ondersteuning | Tijdelijke opslag (GiB) | Maximaal aantal gegevensschijven | Maximum aantal Ethernet Nic's |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Alles | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>H-serie
Virtuele machines uit de H-serie zijn geoptimaliseerd voor toepassingen die worden aangedreven door hoge CPU-frequenties of grote geheugen per kern vereisten. Met de H-serie Vm's functie 8 of 16 Intel Xeon E5 2667 v3-processor kernen, tot 14 GB aan RAM per CPU-kern en zonder hyperthreading. Een VM met de H-serie biedt Maxi maal 80 GB/sec. geheugen bandbreedte.

ACU: 290-300

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

| Grootte | vCPU | Processor | Geheugen (GiB) | Geheugen bandbreedte GiB/s | Basis-CPU-frequentie (GHz) | Frequentie van alle kernen (GHz, piek) | Frequentie met één kern geheugen (GHz, piek) | RDMA-prestaties (GiB/s) | MPI-ondersteuning | Tijdelijke opslag (GiB) | Maximaal aantal gegevensschijven | Maximum aantal Ethernet Nic's |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5 2667 v3 | 56 | 40 | 3,2 | 3,3 | 3.6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3,3 | 3.6 |  - | Intel 5. x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5 2667 v3 | 112 | 40 | 3,2 | 3,3 | 3.6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3,3 | 3.6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3,3 | 3.6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3,3 | 3.6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> voor mpi-toepassingen is het gereserveerde RDMA-back-end-netwerk ingeschakeld door het FDR InfiniBand-netwerk.

<br>
