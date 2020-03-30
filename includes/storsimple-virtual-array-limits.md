---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176066"
---
| **Limiet-id** | **Limiet** | **Opmerkingen** |
| --- | --- | --- |
| Totale capaciteit (inclusief cloud) |Tot 64 TB per virtueel apparaat |U falen over een volledige StorSimple Virtual Array naar een andere lege array. Als u probeert te herstellen naar hetzelfde apparaat, moet u ervoor zorgen dat u voldoende ruimte op het apparaat hebt om deze bewerking te voltooien. Nadat u meer dan 32 TB hebt overschreden, u niet meer naar hetzelfde apparaat herstellen. |
| Maximaal aantal opslagaccountreferenties per apparaat |1 | |
| Maximum aantal volumes/aandelen |16 | |
| Minimale grootte van een gedifferentieerd aandeel |500 GB | |
| Minimumgrootte van een gelaagd volume |500 GB | |
| Maximale grootte van een gelaagd aandeel |20 TB | |
| Maximale grootte van een gelaagd volume |5 TB | |
| Minimale grootte van een lokaal vastgemaakt aandeel |50 GB | |
| Minimale grootte van een lokaal vastgemaakt volume |50 GB | |
| Maximale grootte van een lokaal vastgemaakt aandeel |2 TB | |
| Maximale grootte van een lokaal vastgemaakt volume |200 GB | |
| Maximum aantal iSCSI-verbindingen van initiators |512 | |
| Maximum aantal toegangscontrolerecords per apparaat |64 | |
| Maximaal aantal back-ups dat door het virtuele apparaat wordt bewaard in de map *.backups* voor bestandsserver |5 |Dit omvat de meest recente geplande (gegenereerd door het standaard back-upbeleid) en handmatige back-ups. |
| Maximaal aantal geplande back-ups die door het apparaat worden bewaard |55 |30 dagelijkse back-ups<br>12 maandelijkse back-ups<br>13 jaarlijkse back-ups |
| Maximaal aantal handmatige back-ups die door het apparaat worden bewaard |45 | |
| Maximaal aantal bestanden per aandeel voor een bestandsserver |1 miljoen |Bij het herstellen van een apparaat zijn de hersteltijden evenredig aan het aantal bestanden in alle shares op het apparaat. |
| Maximaal aantal bestanden per volume voor een iSCSI-server |1 miljoen | |
| Maximaal aantal bestanden per virtuele array |4 miljoen | |
| Hersteltijd herstellen |Snel herstellen |Het herstel is gebaseerd op de warmtekaart en is afhankelijk van de volumegrootte.<br>Back-upbewerkingen kunnen plaatsvinden terwijl een herstelbewerking aan de gang is. |

