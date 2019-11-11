---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 0fda881b805eb3a967cf3b05f6c6df8c65d20730
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905778"
---
| Resource | Doel        |
|----------|---------------|
| Maximale grootte van één BLOB-container | Hetzelfde als de maximale capaciteit van het opslag account |
| Maximum aantal blokken in een blok-BLOB of toevoeg-BLOB | 50.000 blokken |
| Maximale grootte van een blok in een blok-BLOB | 100-MiB |
| Maximale grootte van een blok-BLOB | 50.000 X 100 MiB (ongeveer 4,75 TiB) |
| Maximale grootte van een blok in een toevoeg-BLOB | 4 MiB |
| Maximale grootte van een toevoeg-BLOB | 50.000 x 4-MiB (ongeveer 195 GiB) |
| Maximale grootte van een pagina-BLOB | 8 TiB |
| Maximum aantal opgeslagen toegangs beleid per BLOB-container | 5 |
|Aantal doel aanvragen voor één BLOB | Maxi maal 500 aanvragen per seconde |
|Doel doorvoer voor een blob met één pagina | Maxi maal 60 MiB per seconde |
|Doel doorvoer voor een enkele blok-BLOB |Tot Maxi maal limieten voor opslag accounts/uitgaand verkeer<sup>1</sup> |

<sup>1</sup> door Voer van één object is afhankelijk van verschillende factoren, waaronder, maar niet beperkt tot: gelijktijdigheid, aanvraag grootte, prestatie-laag, snelheid van bron voor uploads en bestemming voor down loads. Als u gebruik wilt maken van de prestaties van een [blok-blob met hoge door Voer](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) , gebruikt u een put-BLOB of een put-aanvraag grootte van > 4 MiB (> 256 KiB voor Premium-performance Block Blob Storage of voor data Lake Storage Gen2).
