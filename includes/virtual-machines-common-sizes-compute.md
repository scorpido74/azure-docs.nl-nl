---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 21b982389b186e949b21352f4b11bd6b4aa06dcb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279164"
---
<!-- F-series, Fs-series* -->

De grootte van de virtuele machines met reken capaciteit is hoog voor CPU-geheugen. Deze grootten zijn geschikt voor webservers, netwerk apparaten, batch processen en toepassings servers met een gemiddeld verkeer. Dit artikel bevat informatie over het aantal Vcpu's, gegevens schijven en Nic's. Het bevat ook informatie over opslag doorvoer en netwerk bandbreedte voor elke grootte in deze groep.

De Fsv2-serie is gebaseerd op de Intel® Xeon® Platinum 8168-processor. De IT-service heeft een zeer hoge Turbo klok snelheid van 3,4 GHz en een maximale Turbo frequentie van 3,7 GHz met één kern. Intel® AVX-512-instructies zijn nieuw op schaal bare Intel-processors. Deze instructies bieden een 2X hoge prestatie verbetering van de werk belasting voor vector verwerking op bewerkingen met een drijvende komma van zowel één als dubbele precisie. Met andere woorden, ze zijn heel snel voor elke reken werk belasting.

Voor een lagere prijs per uur is de Fsv2-serie de beste prijs-prestatie verhouding in de Azure-Port Folio op basis van de ACU (Azure Compute Unit) per vCPU.

## <a name="fsv2-series-sup1sup"></a>Fsv2-serie <sup>1</sup>

ACU: 195 - 210

Premium Storage: ondersteund

Premium Storage caching: ondersteund

| Grootte             | Vcpu's | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximaal aantal gegevensschijven | Maximale doorvoer voor schijven met caching en tijdelijke opslag: IOPS / MBps (cachegrootte in GiB) | Max. doorvoer voor schijf zonder caching: IOPS/MBps | Maximum aantal Nic's/verwachte netwerk bandbreedte (Mbps) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000/31 (32)           | 3200/47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000/63 (64)           | 6400/95                | 2 / 1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000/127 (128)        | 12800/190              | 4 / 3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000/255 (256)        | 25600/380              | 4 / 7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000/512 (512)        | 51200/750              | 8 / 14000              |
| Standard_F48s_v2 | 48     | 96          | 384            | 32             | 96000/768 (768)        | 76800/1100             | 8 / 21000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000/1024 (1024)     | 80000/1100             | 8 / 28000              |
| Standard_F72s_v2<sup>2,&nbsp;3</sup> | 72 | 144 | 576         | 32             | 144000/1152 (1520)     | 80000/1100             | 8 / 30000              |

<sup>1</sup> virtuele machines uit de Fsv2-serie bieden een Intel® Hyper-Threading-technologie.

<sup>2</sup> voor het gebruik van meer dan 64 vCPU is een van deze ondersteunde gast besturingssystemen vereist: Windows Server 2016, Ubuntu 16,04 LTS, SLES 12 SP2 en Red Hat Enterprise Linux, CentOS 7,3 of Oracle Linux 7,3 met Lis 4.2.1.

<sup>3</sup> exemplaar is geïsoleerd voor hardware die is toegewezen aan één klant.
