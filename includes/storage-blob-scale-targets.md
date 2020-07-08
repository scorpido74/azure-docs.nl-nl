---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/23/2020
ms.author: tamram
ms.openlocfilehash: 43a72d915fa5a00c54bef7a06fe3815a7d63f2fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805785"
---
| Resource | Doel | Doel (preview-versie) |
|-|-|-|
| Maximale grootte van één BLOB-container | Hetzelfde als de maximale capaciteit van het opslag account |  |
| Maximum aantal blokken in een blok-BLOB of toevoeg-BLOB | 50.000 blokken |  |
| Maximale grootte van een blok in een blok-BLOB | 100-MiB | 4000-MiB (preview-versie) |
| Maximale grootte van een blok-BLOB | 50.000 X 100 MiB (ongeveer 4,75 TiB) | 50.000 X 4000 MiB (ongeveer 190,7 TiB) (preview) |
| Maximale grootte van een blok in een toevoeg-BLOB | 4 MiB |  |
| Maximale grootte van een toevoeg-BLOB | 50.000 x 4-MiB (ongeveer 195 GiB) |  |
| Maximale grootte van een pagina-BLOB | 8 TiB |  |
| Maximum aantal opgeslagen toegangs beleid per BLOB-container | 5 |  |
| Aantal doel aanvragen voor één BLOB | Maxi maal 500 aanvragen per seconde |  |
| Doel doorvoer voor een blob met één pagina | Maxi maal 60 MiB per seconde |  |
| Doel doorvoer voor een enkele blok-BLOB | Tot Maxi maal limieten voor opslag accounts/uitgaand verkeer<sup>1</sup> |  |

<sup>1</sup> door Voer voor één blob is afhankelijk van verschillende factoren, waaronder, maar niet beperkt tot: gelijktijdigheid, aanvraag grootte, prestatie-laag, snelheid van bron voor uploads en bestemming voor down loads. Als u gebruik wilt maken van de prestatie verbeteringen van [blok-blobs met hoge door Voer](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/), uploadt u grotere blobs of blokken. Roep met name de [put BLOB](/rest/api/storageservices/put-blob) of [put](/rest/api/storageservices/put-block) -bewerking aan met een BLOB of blok grootte die groter is dan 4 MIB voor standaard opslag accounts. Gebruik voor Premium-blok-BLOB of voor Data Lake Storage Gen2 opslag accounts een blok-of Blob-grootte die groter is dan 256 KiB.

In de volgende tabel worden de maximum grootte van het blok en de BLOB beschreven die is toegestaan door de service versie.

| Service versie | Maximale blok grootte (via put-blok) | Maximale grootte van BLOB (via de lijst voor blok keren) | Maximale Blob-grootte via één schrijf bewerking (via een put-blob) |
|-|-|-|-|
| Versie 2019-12-12 en hoger | 4000-MiB (preview-versie) | Ongeveer 190,7 TiB (4000 MiB X 50.000-blokken) (preview) | 5000-MiB (preview-versie) |
| Versie 2016-05-31 tot en met versie 2019-07-07 | 100-MiB | Ongeveer 4,75 TiB (100 MiB X 50.000 blokken) | 256-MiB |
| Eerdere versies dan 2016-05-31 | 4 MiB | Ongeveer 195 GiB (4 MiB X 50.000 blokken) | 64-MiB |
