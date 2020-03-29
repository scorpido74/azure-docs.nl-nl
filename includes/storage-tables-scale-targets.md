---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78940965"
---
In de volgende tabel worden de capaciteits-, schaalbaarheids- en prestatiedoelen voor tabelopslag beschreven.

| Resource | Doel |
|----------|---------------|
| Aantal tabellen in een Azure-opslagaccount | Alleen beperkt door de capaciteit van het opslagaccount |
| Aantal partities in een tabel | Alleen beperkt door de capaciteit van het opslagaccount |
| Aantal entiteiten in een partitie | Alleen beperkt door de capaciteit van het opslagaccount |
| Maximale grootte van één tabel | 500 TiB |
| Maximale grootte van één entiteit, inclusief alle eigenschapswaarden | 1 MiB |
| Maximum aantal eigenschappen in een tabelentiteit | 255 (inclusief de drie systeemeigenschappen, **PartitionKey,** **RowKey**en **Timestamp)** |
| Maximale totale grootte van een afzonderlijke eigenschap in een entiteit | Verschilt per eigenschapstype. Zie **Eigenschaptypen** in [Het gegevensmodel van de tabelservice voor](/rest/api/storageservices/understanding-the-table-service-data-model)meer informatie . |
| Grootte van de **partitionkey** | Een tekenreeks tot 1 KiB in grootte |
| Grootte van de **RowKey** | Een tekenreeks tot 1 KiB in grootte |
| Grootte van een entiteitsgroepstransactie | Een transactie kan maximaal 100 entiteiten omvatten en het laadvermogen moet minder dan 4 MiB groot zijn. Een entiteitsgroeptransactie kan slechts één keer een update naar een entiteit bevatten. |
| Maximaal aantal opgeslagen toegangsbeleid per tabel | 5 |
| Maximumaanvraagtarief per opslagaccount | 20.000 transacties per seconde, die een 1-KiB entiteit grootte veronderstelt |
| Doeldoorvoer voor één tabelpartitie (1 KiB-entiteiten) | Maximaal 2.000 entiteiten per seconde |