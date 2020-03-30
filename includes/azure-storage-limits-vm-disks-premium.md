---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334976"
---
**Premium onbeheerde virtuele machineschijven: limieten per account**

| Resource | Limiet |
| --- | --- |
| Totale schijfcapaciteit per account |35 TB |
| Totale momentopnamecapaciteit per account |10 TB |
| Maximale bandbreedte per account (binnendringen + uitgang)<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*Ingress* verwijst naar alle gegevens van verzoeken die naar een opslagaccount worden verzonden. *Uitgang* verwijst naar alle gegevens van reacties die worden ontvangen van een opslagaccount.

**Premium onbeheerde virtuele machineschijven: limieten per schijf**

| Schijftype voor Premium Storage | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Schijfgrootte |128 GiB |512 GiB |1.024 GiB (1 TB) |2.048 GiB (2 TB)|4.095 GiB (4 TB)|
| Maximale IOPS per schijf |500 |2.300 |5.000 |7.500 |7.500 |
| Maximale doorvoer per schijf |100 MB/sec | 150 MB/sec |200 MB/sec |250 MB/sec |250 MB/sec |
| Maximum aantal schijven per opslagaccount |280 |70 |35 | 17 | 8 |

**Premium onbeheerde virtuele machineschijven: per-VM-limieten**

| Resource | Limiet |
| --- | --- |
| Maximale IOPS per VM |80.000 IOPS met GS5 VM |
| Maximale doorvoer per VM |2.000 MB/s met GS5 VM |

