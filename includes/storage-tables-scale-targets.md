---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78940965"
---
In de volgende tabel worden de capaciteit, schaal baarheid en prestatie doelen voor tabel opslag beschreven.

| Resource | Doel |
|----------|---------------|
| Aantal tabellen in een Azure-opslag account | Alleen beperkt door de capaciteit van het opslag account |
| Aantal partities in een tabel | Alleen beperkt door de capaciteit van het opslag account |
| Aantal entiteiten in een partitie | Alleen beperkt door de capaciteit van het opslag account |
| Maximale grootte van één tabel | 500 TiB |
| Maximale grootte van één entiteit, inclusief alle eigenschaps waarden | 1 MiB |
| Maximum aantal eigenschappen in een tabel entiteit | 255 (inclusief de drie systeem eigenschappen, **PartitionKey**, **RowKey**en **tijds tempel**) |
| Maximale totale grootte van een afzonderlijke eigenschap in een entiteit | Is afhankelijk van het eigenschaps type. Zie **eigenschaps typen** voor meer informatie over [het tabel service gegevens model](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Grootte van de **PartitionKey** | Een teken reeks met een grootte van Maxi maal 1 KiB |
| Grootte van de **RowKey** | Een teken reeks met een grootte van Maxi maal 1 KiB |
| Grootte van de trans actie van een entiteits groep | Een trans actie kan Maxi maal 100 entiteiten bevatten en de payload moet kleiner zijn dan 4 MiB-grootte. Een trans actie van een entiteits groep kan slechts eenmaal een update aan een entiteit bevatten. |
| Maximum aantal opgeslagen toegangs beleid per tabel | 5 |
| Maximum aantal aanvragen per opslag account | 20.000 trans acties per seconde, waarbij een 1-KiB entiteits grootte wordt aangenomen |
| Doel doorvoer voor één tabel partitie (1 KiB-entiteiten) | Maxi maal 2.000 entiteiten per seconde |