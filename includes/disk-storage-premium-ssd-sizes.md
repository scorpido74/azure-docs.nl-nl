---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8bb607d0b3091de34c5c75b09c3b99257f6b2c2d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467754"
---
| Premium-SSD grootten | P1 | P2 | P3 | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Schijf grootte in GiB | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1\.024 | 2\.048 | 4\.096 | 8\.192 | 16.384 | 32.767 |
| IOP's per schijf | 120 | 120 | 120 | 120 | 240 | 500 | 1\.100 | 2\.300 | 5\.000 | 7\.500 | 7\.500 | 16.000 | 18.000 | 20.000 |
| Doorvoer per schijf | 25 MiB per seconde | 25 MiB per seconde | 25 MiB per seconde |Maxi maal 25 MiB per seconde | 50-MiB per seconde | 100-MiB per seconde | 125-MiB per seconde | 150-MiB per seconde | 200-MiB per seconde | 250-MiB per seconde | 250-MiB per seconde| 500-MiB per seconde | 750-MiB per seconde | 900-MiB per seconde |
| Max burst IOPS per schijf * * | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 |
| Maximale burst-door Voer per schijf * * | 170-MiB per seconde | 170-MiB per seconde | 170-MiB per seconde | 170-MiB per seconde | 170-MiB per seconde | 170-MiB per seconde | 170-MiB per seconde | 170-MiB per seconde |
| Maximale burst-duur * * | 30 minuten  | 30 minuten  | 30 minuten  | 30 minuten  | 30 minuten  | 30 minuten  | 30 minuten  | 30 minuten  |

\*geeft een schijf grootte aan die momenteel als preview-versie beschikbaar is. regionale beschikbaarheids informatie Zie [Premium-schijven: beheerd en onbeheerd](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#premium-disks-managed-and-unmanaged).

\** geeft een functie aan die momenteel als preview-versie beschikbaar is. Zie [schijf bursting](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) voor meer informatie.
