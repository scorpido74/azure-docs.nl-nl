---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 6cf9be653da2dd587b93724b6e319dc4d20686c2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536426"
---
| Resource | Standaardbestandsaandelen | Premium bestandsaandelen |
|----------|---------------|------------------------------------------|
| Minimale grootte van een bestandsshare | Geen minimum; betalen als je gaat | 100 GiB; Ingericht |
| Maximale grootte van een bestandsshare | 100 TiB*, 5 tiB | 100 TiB |
| Maximale grootte van een bestand in een bestandsshare | 1 TiB | 1 TiB |
| Maximaal aantal bestanden in een bestandsshare | Geen limiet | Geen limiet |
| Maximale IOPS per aandeel | 10.000 IOPS*, 1.000 IOPS | 100.000 IOPS |
| Maximaal aantal opgeslagen toegangsbeleidsregels per bestandsshare | 5 | 5 |
| Doeldoorvoer voor één bestandsshare | tot 300 MiB/sec*, tot 60 MiB/sec ,  | Zie de invallen en uitgaande waarden voor het delen van premium bestanden|
| Maximale uitgang voor één bestandsshare | Bekijk de standaarddoeldoorvoer van bestandsshare | Tot 6.204 MiB/s |
| Maximale ingress voor één bestandsshare | Bekijk de standaarddoeldoorvoer van bestandsshare | Maximaal 4.136 MiB/s |
| Maximale open handgrepen per bestand | 2.000 open handgrepen | 2.000 open handgrepen |
| Maximaal aantal momentopnamen voor delen | 200 momentopnamen delen | 200 momentopnamen delen |
| Maximale object (mappen en bestanden) naamlengte | 2.048 tekens | 2.048 tekens |
| Maximale component padnaam (in het pad \A\B\C\D, elke letter is een component) | 255 tekens | 255 tekens |

\*Standaard op standaard bestandsshares is 5 TiB, zie [Grote bestandsshares inschakelen en maken](../articles/storage/files/storage-files-how-to-create-large-file-share.md) voor de details over hoe u de standaardbestandsshares verhogen tot 100 TiB.
