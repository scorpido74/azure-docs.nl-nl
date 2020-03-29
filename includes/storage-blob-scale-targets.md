---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75392283"
---
| Resource | Doel        |
|----------|---------------|
| Maximale grootte van één blobcontainer | Hetzelfde als de maximale opslagcapaciteit van het opslagaccount |
| Maximaal aantal blokken in een blokblob of toevoegen blob | 50.000 blokken |
| Maximale grootte van een blok in een blokblob | 100 MiB |
| Maximale grootte van een blokblob | 50.000 X 100 MiB (ongeveer 4,75 TiB) |
| Maximale grootte van een blok in een toevoegende blob | 4 MiB |
| Maximale grootte van een toevoegende blob | 50.000 x 4 MiB (ongeveer 195 GiB) |
| Maximale grootte van een paginablob | 8 TiB |
| Maximaal aantal opgeslagen toegangsbeleidsregels per blobcontainer | 5 |
|Doelaanvraagpercentage voor één blob | Maximaal 500 aanvragen per seconde |
|Doeldoorvoer voor één paginablob | Tot 60 MiB per seconde |
|Doeldoorvoer voor één blokblob |Limieten voor opslagaccount ingress/uitgang1<sup>1</sup> |

<sup>1</sup> Doorvoer voor een enkele blob is afhankelijk van verschillende factoren, waaronder, maar niet beperkt tot: gelijktijdigheid, aanvraaggrootte, prestatielaag, snelheid van bron voor uploads en bestemming voor downloads. Upload grotere blobs of blokken om te profiteren van de prestatieverbeteringen van [blokkeerblobs](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)met hoge doorvoer. Noem specifiek de bewerking [Put Blob](/rest/api/storageservices/put-blob) of [Put Block](/rest/api/storageservices/put-block) met een blob- of blokgrootte die groter is dan 4 MiB voor standaardopslagaccounts. Gebruik een blok- of blobgrootte van meer dan 256 KiB voor premium blokblobs of voor Opslagaccounts van Data Lake Storage Gen2.
