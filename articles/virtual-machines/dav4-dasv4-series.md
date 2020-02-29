---
title: Dav4 en Dasv4-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines uit de Dav4-en Dasv4-serie.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 809843bddc4216df4dab44330172d36778248b1a
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78162176"
---
# <a name="dav4-and-dasv4-series"></a>Dav4- en Dasv4-serie

De Dav4-serie en de Dasv4-serie zijn nieuwe grootten die gebruikmaken van de 2.35 GHz EPYC<sup>TM</sup> 7452-processor van AMD in een configuratie met meerdere threads, met maxi maal 256 MB L3-cache van 8 GB aan de L3-cache tot elke 8 kernen die de klant opties verhogen voor het uitvoeren van hun werk belastingen voor algemeen gebruik. De Dav4-serie en de Dasv4-serie hebben dezelfde geheugen-en schijf configuraties als de D & Dsv3-serie.

## <a name="dav4-series"></a>Dav4-serie

ACU: 230-260

Premium Storage: niet ondersteund

Premium Storage caching: niet ondersteund

Livemigratie: ondersteund

Updates voor geheugen behoud: ondersteund

De grootte van de Dav4-serie is gebaseerd op de 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processor die een maximale maximum frequentie van 3.35 GHz kan bereiken. De grootte van de Dav4-serie biedt een combi natie van vCPU, geheugen en tijdelijke opslag voor de meeste productiewerk belastingen. Gegevensschijfopslag wordt apart van virtuele machines in rekening gebracht. Als u Premium SSD wilt gebruiken, gebruikt u de Dasv4-grootten. De prijs-en facturerings meters voor Dasv4-grootten zijn gelijk aan die van de Dav4-serie.

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale tijdelijke opslagdoorvoer: IOPS / MBps lezen / MBps schrijven | Maximum aantal Nic's/verwachte netwerk bandbreedte (MBps) |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000 / 46 / 23   | 2 / 1000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000 / 93 / 46   | 2 / 2000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12.000 / 187 / 93 | 4 / 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24.000 / 375 / 187 |8 / 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48.000 / 750 / 375 |8 / 16000 |
| Standard_D48a_v4 <sup>**</sup> | 48 | 192| 1200 | 32 | | |
| Standard_D64a_v4 <sup>**</sup> | 64 | 256 | 1600 | 32 | | |
| Standard_D96a_v4 <sup>**</sup> | 96 | 384 | 2400 | 32 | | |

<sup>**</sup> Deze grootten zijn beschikbaar als preview-versie.  Als u geïnteresseerd bent in het proberen van deze grotere grootten, meldt u zich aan bij [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

## <a name="dasv4-series"></a>Dasv4-serie

ACU: 230-260

Premium Storage: ondersteund

Premium Storage caching: ondersteund

Livemigratie: ondersteund

Updates voor geheugen behoud: ondersteund

De grootte van de Dasv4-serie is gebaseerd op de 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processor die een hogere maximum frequentie van 3.35 GHz kan bereiken en Premium SSD kan gebruiken. De grootte van de Dasv4-serie biedt een combi natie van vCPU, geheugen en tijdelijke opslag voor de meeste productiewerk belastingen.

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000/32 (50)|3200/48|2 / 1000 |
| Standard_D4as_v4|4|16|32|8|8000/64 (100)|6400/96|2 / 2000 |
| Standard_D8as_v4|8|32|64|16|16000/128 (200)|12800/192|4 / 4000 |
| Standard_D16as_v4|16|64|128|32|32000/255 (400)|25600 / 384|8 / 8000 |
| Standard_D32as_v4|32|128|256|32|64000/510 (800)|51200/768|8 / 16000 |
| Standard_D48as_v4 <sup>**</sup>|48|192|384|32| | | 
| Standard_D64as_v4 <sup>**</sup>|64|256|512|32| | | 
| Standard_D96as_v4 <sup>**</sup>|96|384|768|32| | | 

<sup>**</sup> Deze grootten zijn beschikbaar als preview-versie.  Als u geïnteresseerd bent in het proberen van deze grotere grootten, meldt u zich aan bij [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview).

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Andere grootten

- [Algemeen doel](sizes-general.md)
- [Geoptimaliseerd geheugen](sizes-memory.md)
- [Geoptimaliseerde opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
