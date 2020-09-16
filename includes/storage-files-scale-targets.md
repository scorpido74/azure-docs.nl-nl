---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 3d4cc17570057f5f37cf38685847afbe38ea6831
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90606613"
---
| Resource | Standaardbestandsshares | Premiumbestandsshares |
|----------|---------------|------------------------------------------|
| Minimale grootte van een bestandsshare | Geen minimum; betalen naar gebruik | 100 GiB; ingericht |
| Maximale grootte van een bestandsshare | 100 TiB *, 5 TiB | 100 TiB |
| Maximale grootte van een bestand in een bestandsshare | 1 TiB | 4 TiB |
| Maximum aantal bestanden in een bestandsshare | Geen limiet | Geen limiet |
| Maximum aantal IOPS per share | 10.000 IOPS*, 1000 IOPS of 100 aanvragen in 100 ms | 100.000 IOPS |
| Maximaal aantal opgeslagen toegangsbeleidsregels per bestandsshare | 5 | 5 |
| Doeldoorvoer voor één bestandsshare | maximaal 300 MiB/sec *, maximaal 60 MiB/sec,  | Bekijk de inkomende en uitgaande waarden voor premiumbestandsshares|
| Maximum aantal uitgaande waarden voor één bestandsshare | Bekijk de standaard doeldoorvoer voor bestandsshares | Maximaal 6204 MiB/s |
| Maximum aantal inkomende waarden voor één bestandsshare | Bekijk de standaard doeldoorvoer voor bestandsshares | Maximaal 4136 MiB/s |
| Maximum aantal open ingangen per bestand of map | 2000 open ingangen | 2000 open ingangen |
| Maximum aantal momentopnamen van shares | 200 momentopnamen van shares | 200 momentopnamen van shares |
| Maximum lengte van de naam van het object (mappen en bestanden) | 2048 tekens | 2048 tekens |
| Maximum pathname-onderdeel (in het pad \A\B\C\D is elke letter een onderdeel) | 255 tekens | 255 tekens |
| Limiet voor vaste koppelingen (alleen NFS) | N.v.t. | 178 |

\* Standaardbestandsshares zijn 5 TiB. Zie [Grote bestandsshares inschakelen en maken](../articles/storage/files/storage-files-how-to-create-large-file-share.md) voor meer informatie over hoe u de standaardbestandsshares kunt uitbreiden tot 100 TiB.
