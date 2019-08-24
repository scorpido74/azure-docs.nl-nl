---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 05/16/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 632b23a726b8e006152662ef7814aa4b7ef887fc
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69984533"
---
Deze sectie bevat informatie over vorige generaties virtuele machine grootten. Deze grootten kunnen nog steeds worden gebruikt, maar er zijn nieuwere generaties beschikbaar. 

## <a name="f-series"></a>F-serie

De F-serie is gebaseerd op de 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell)-processor, die met Intel Turbo Boost Technology 2.0 een kloksnelheid kan halen van 3,1 GHz. Dit zijn dezelfde CPU-prestaties als de virtuele machines uit de Dv2-serie.  

Virtuele machines uit de F-serie vormen een uitstekende keuze voor workloads die snellere CPU's nodig hebben, maar niet zo veel geheugen of tijdelijke opslag per vCPU.  Voor workloads voor analysen, gameservers, webservers en batchverwerking is de F-serie wellicht een betere keuze.

ACU 210-250

Premium-opslag:  Niet ondersteund

Premium Storage caching:  Niet ondersteund

| Size         | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale tijdelijke opslag doorvoer: IOPS/Lees MBps/MBps schrijven | Maximum aantal gegevens schijven/door Voer: IOPS | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3000 / 46 / 23                                           | 4 / 4 x 500                         | 2 / 750                 |
| Standard_F2  | 2         | 4           | 32             | 6000 / 93 / 46                                           | 8 / 8 x 500                         | 2 / 1500                     |
| Standard_F4  | 4         | 8           | 64             | 12.000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standard_F8  | 8         | 16          | 128            | 24.000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standard_F16 | 16        | 32          | 256            | 48.000 / 750 / 375                                        | 64/64x500                       | 8 / 12000           |

## <a name="fs-series-sup1sup"></a>FS-serie <sup>1</sup>

De Fs-serie biedt alle voordelen van de F-serie, plus Premium-opslag.

ACU 210-250

Premium-opslag:  Ondersteund

Premium Storage caching:  Ondersteund

| Size | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maximale doorvoer voor opslag in cache en tijdelijke opslag: IOPS/MBps (cache grootte in GiB) | Maximale doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4000/32 (12) |3200/48 |2 / 750 |
| Standard_F2s |2 |4 |8 |8 |8000/64 (24) |6400/96 |2 / 1500 |
| Standard_F4s |4 |8 |16 |16 |16000/128 (48) |12800/192 |4 / 3000 |
| Standard_F8s |8 |16 |32 |32 |32000 / 256 (96) |25600 / 384 |8 / 6000 |
| Standard_F16s |16 |32 |64 |64 |64000/512 (192) |51200/768 |8 / 12000 |

MBps = 10^6 bytes per seconde en GiB = 1024^3 bytes.

<sup>1</sup> de maximale schijf doorvoer (IOPS of Mbps) die mogelijk is met een virtuele machine uit de FS-serie kan worden beperkt door het aantal, de grootte en de striping van de gekoppelde schijven.  Zie [ontwerpen voor hoge prestaties](../articles/virtual-machines/windows/premium-storage-performance.md)voor meer informatie.  

## <a name="nvv2-series"></a>NVv2-serie

**Aanbeveling voor nieuwere grootte**: [NVv3-serie](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv3-series--1)

De virtuele machines uit de NVv2-serie worden aangedreven door [Nvidia Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) gpu's en de NVIDIA Grid-technologie met Intel Broadwell cpu's. Deze virtuele machines zijn gericht op GPU-versnelde grafische toepassingen en virtuele Bureau bladen waar klanten hun gegevens willen visualiseren, de resultaten kunnen simuleren om te bekijken, te werken met CAD of inhoud te genereren en te streamen. Daarnaast kunnen deze virtuele machines enkelvoudige, nauwkeurige workloads uitvoeren zoals encoding en renderen. Virtuele machines van NVv2 ondersteunen Premium Storage en komen twee maal zoveel systeem geheugen (RAM) te staan in vergelijking met de bijbehorende voorafgaande NV-serie.  

Elke GPU in NVv2-exemplaren wordt geleverd met een GRID-licentie. Deze licentie geeft u de flexibiliteit om een NV-exemplaar te gebruiken als een virtueel werk station voor één gebruiker, of 25 gelijktijdige gebruikers kunnen verbinding maken met de virtuele machine voor een virtuele-toepassings scenario.

| Size | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | GPU | GPU-geheugen: GiB | Max. aantal gegevensschijven | Max. aantal NIC's | Virtuele werk stations | Virtuele toepassingen | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |
