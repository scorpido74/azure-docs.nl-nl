---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "67457305"
---
| Resource | Doel |
|----------|---------------|
| Maximale grootte van één tabel | 500 TiB |
| Maximale grootte van een tabel entiteit | 1 MiB |
| Maximum aantal eigenschappen in een tabel entiteit | 255, dat drie systeem eigenschappen bevat: PartitionKey, RowKey en Time Stamp |
| Maximale totale grootte van eigenschaps waarden in een entiteit | 1 MiB |
| Maximale totale grootte van een afzonderlijke eigenschap in een entiteit | Is afhankelijk van het eigenschaps type. Zie **eigenschaps typen** voor meer informatie over [het tabel service gegevens model](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Maximum aantal opgeslagen toegangs beleid per tabel | 5 |
| Maximum aantal aanvragen per opslag account | 20.000 trans acties per seconde, waarbij een 1-KiB entiteits grootte wordt aangenomen |
| Doel doorvoer voor één tabel partitie (1 KiB-entiteiten) | Maxi maal 2.000 entiteiten per seconde |