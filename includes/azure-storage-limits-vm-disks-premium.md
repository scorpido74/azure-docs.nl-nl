---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80334976"
---
**Premium niet-beheerde schijven van virtuele machines: limieten per account**

| Resource | Limiet |
| --- | --- |
| Totale schijfcapaciteit per account |35 TB |
| Totale momentopnamecapaciteit per account |10 TB |
| Maximale band breedte per account (ingangs + uitgang)<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*ingang* verwijst naar alle gegevens van aanvragen die worden verzonden naar een opslag account. Uitgaand *verwijst naar* alle gegevens van antwoorden die worden ontvangen van een opslag account.

**Premium niet-beheerde schijven van virtuele machines: limieten per schijf**

| Schijftype voor Premium Storage | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Schijfgrootte |128 GiB |512 GiB |1.024 GiB (1 TB) |2.048 GiB (2 TB)|4.095 GiB (4 TB)|
| Maximum aantal IOPS per schijf |500 |2\.300 |5\.000 |7\.500 |7\.500 |
| Maximale door Voer per schijf |100 MB/sec | 150 MB/sec |200 MB/sec |250 MB/sec |250 MB/sec |
| Maximum aantal schijven per opslag account |280 |70 |35 | 17 | 8 |

**Premium niet-beheerde schijven van virtuele machines: limieten per VM**

| Resource | Limiet |
| --- | --- |
| Maximum aantal IOPS per VM |80.000 IOPS met GS5 VM |
| Maximale door Voer per VM |2.000 MB/sec. met GS5 VM |

