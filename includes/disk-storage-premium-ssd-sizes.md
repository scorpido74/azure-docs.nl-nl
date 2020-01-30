---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7b2d4777772d842898cfcdd04f1c6d926cdbf0ad
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76837558"
---
| Premium-SSD grootten | P1 | P2 | P3 | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Schijf grootte in GiB | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1,024 | 2\.048 | 4\.096 | 8\.192 | 16.384 | 32.767 |
| IOPS per schijf | 120 | 120 | 120 | 120 | 240 | 500 | 1\.100 | 2\.300 | 5\.000 | 7\.500 | 7\.500 | 16,000 | 18.000 | 20,000 |
| Doorvoer per schijf | 25 MiB per seconde | 25 MiB per seconde | 25 MiB per seconde | 25 MiB per seconde | 50-MiB per seconde | 100-MiB per seconde | 125-MiB per seconde | 150-MiB per seconde | 200-MiB per seconde | 250-MiB per seconde | 250-MiB per seconde| 500-MiB per seconde | 750-MiB per seconde | 900-MiB per seconde |
| Max burst IOPS per schijf * * | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 | 3\.500 |
| Maximale burst-door Voer per schijf * * | 170-MiB per seconde | 170-MiB per seconde | 170-MiB per seconde | 170-MiB per seconde | 170-MiB per seconde | 170-MiB per seconde | 170-MiB per seconde | 170-MiB per seconde |
| Maximale burst-duur * * | 30 minuten  | 30 minuten  | 30 minuten  | 30 minuten  | 30 minuten  | 30 minuten  | 30 minuten  | 30 minuten  |
| Geschikt voor reserve ring | Nee  | Nee  | Nee  | Nee  | Nee  | Nee  | Nee  | Nee  | Ja, Maxi maal één jaar | Ja, Maxi maal één jaar | Ja, Maxi maal één jaar | Ja, Maxi maal één jaar | Ja, Maxi maal één jaar | Ja, Maxi maal één jaar |

\*geeft een schijf grootte aan die momenteel als preview-versie beschikbaar is, voor regionale beschikbaarheids informatie Zie [nieuwe schijf grootten: beheerd en onbeheerd](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged).  
\*\*geeft een functie aan die momenteel als preview-versie beschikbaar is, Zie [schijf bursting](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) voor meer informatie.
