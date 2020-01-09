---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392283"
---
| Bron | Kiezen        |
|----------|---------------|
| Maximale grootte van één BLOB-container | Hetzelfde als de maximale capaciteit van het opslag account |
| Maximum aantal blokken in een blok-BLOB of toevoeg-BLOB | 50.000 blokken |
| Maximale grootte van een blok in een blok-BLOB | 100 MiB |
| Maximale grootte van een blok-BLOB | 50.000 X 100 MiB (ongeveer 4,75 TiB) |
| Maximale grootte van een blok in een toevoeg-BLOB | 4 MiB |
| Maximale grootte van een toevoeg-BLOB | 50.000 x 4-MiB (ongeveer 195 GiB) |
| Maximale grootte van een pagina-BLOB | 8 TiB |
| Maximum aantal opgeslagen toegangs beleid per BLOB-container | 5 |
|Aantal doel aanvragen voor één BLOB | Maxi maal 500 aanvragen per seconde |
|Doel doorvoer voor een blob met één pagina | Maxi maal 60 MiB per seconde |
|Doel doorvoer voor een enkele blok-BLOB |Tot Maxi maal limieten voor opslag accounts/uitgaand verkeer<sup>1</sup> |

<sup>1</sup> door Voer voor één blob is afhankelijk van verschillende factoren, waaronder, maar niet beperkt tot: gelijktijdigheid, aanvraag grootte, prestatie-laag, snelheid van bron voor uploads en bestemming voor down loads. Als u gebruik wilt maken van de prestatie verbeteringen van [blok-blobs met hoge door Voer](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/), uploadt u grotere blobs of blokken. Roep met name de [put BLOB](/rest/api/storageservices/put-blob) of [put](/rest/api/storageservices/put-block) -bewerking aan met een BLOB of blok grootte die groter is dan 4 MIB voor standaard opslag accounts. Gebruik voor Premium-blok-BLOB of voor Data Lake Storage Gen2 opslag accounts een blok-of Blob-grootte die groter is dan 256 KiB.
