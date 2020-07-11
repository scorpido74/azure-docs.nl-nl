---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 9c130fb2e032a24384f52f11957632319093e1e3
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86226391"
---
| Resource | Standaard bestands shares | Premium-bestands shares |
|----------|---------------|------------------------------------------|
| Minimale grootte van een bestands share | Geen minimum; betalen naar gebruik | 100 GiB; ingericht |
| Maximale grootte van een bestands share | 100 TiB *, 5 TiB | 100 TiB |
| Maximale grootte van een bestand in een bestands share | 1 TiB | 1 TiB |
| Maximum aantal bestanden in een bestands share | Geen limiet | Geen limiet |
| Maximum aantal IOPS per share | 10.000 IOPS *, 1.000 IOPS | 100.000 IOPS |
| Maximum aantal opgeslagen toegangs beleid per bestands share | 5 | 5 |
| Doel doorvoer voor één bestands share | Maxi maal 300 MiB/sec *, Maxi maal 60 MiB per seconde,  | Bekijk de ingangs-en uitgangs waarden voor Premium file share|
| Maximum aantal uitgangen voor één bestands share | Bekijk de standaard doel doorvoer voor bestands shares | Maxi maal 6.204 MiB/s |
| Maxi maal aantal binnenkomend verkeer voor één bestands share | Bekijk de standaard doel doorvoer voor bestands shares | Maxi maal 4.136 MiB/s |
| Maximum aantal open ingangen per bestand of map | 2.000 open ingangen | 2.000 open ingangen |
| Maximum aantal moment opnamen van shares | 200 share-moment opnamen | 200 share-moment opnamen |
| Maximum lengte van de naam van het object (directory's en bestanden) | 2.048 tekens | 2.048 tekens |
| Maximum pathname-onderdeel (in het pad \A\B\C\D is elke letter een onderdeel) | 255 tekens | 255 tekens |

\*Standaard bestands shares zijn 5 TiB. Zie [grote bestands shares inschakelen en maken](../articles/storage/files/storage-files-how-to-create-large-file-share.md) voor meer informatie over het uitbreiden van de standaard bestands shares tot 100 Tib.
