---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176433"
---
| Limiet-id | Limiet | Opmerkingen |
| --- | --- | --- |
| Maximaal aantal referenties voor opslagaccount |64 | |
| Maximum aantal volumecontainers |64 | |
| Maximum aantal volumes |255 | |
| Maximaal aantal schema's per bandbreedtesjabloon |168 |Een schema voor elk uur, elke dag van de week. |
| Maximale grootte van een gelaagd volume op fysieke apparaten |64 TB voor StorSimple 8100 en StorSimple 8600 |StorSimple 8100 en StorSimple 8600 zijn fysieke apparaten. |
| Maximale grootte van een gelaagd volume op virtuele apparaten in Azure |30 TB voor StorSimple 8010 <br></br> 64 TB voor StorSimple 8020 |StorSimple 8010 en StorSimple 8020 zijn virtuele apparaten in Azure die respectievelijk gebruikmaken van standaardopslag en Premium-opslag. |
| Maximale grootte van een lokaal vastgemaakt volume op fysieke apparaten |9 TB voor StorSimple 8100 <br></br> 24 TB voor StorSimple 8600 |StorSimple 8100 en StorSimple 8600 zijn fysieke apparaten. |
| Maximum aantal iSCSI-verbindingen |512 | |
| Maximum aantal iSCSI-verbindingen van initiators |512 | |
| Maximum aantal toegangscontrolerecords per apparaat |64 | |
| Maximaal aantal volumes per back-upbeleid |24 | |
| Maximaal aantal back-ups dat per back-upbeleid wordt behouden |64 | |
| Maximaal aantal schema's per back-upbeleid |10 | |
| Maximaal aantal momentopnamen van elk type dat per volume kan worden bewaard |256 |Dit bedrag omvat lokale momentopnamen en cloudsnapshots. |
| Maximaal aantal momentopnamen dat in elk apparaat aanwezig kan zijn |10.000 | |
| Maximaal aantal volumes dat parallel kan worden verwerkt voor back-up, herstel of kloon |16 |<ul><li>Als er meer dan 16 volumes zijn, worden ze opeenvolgend verwerkt als verwerkingssleuven beschikbaar komen.</li><li>Nieuwe back-ups van een gekloond of een hersteld gelaagd volume kunnen pas plaatsvinden nadat de bewerking is voltooid. Voor een lokaal volume zijn back-ups toegestaan nadat het volume online is.</li></ul> |
| Hersteltijd herstellen en klonen voor gelaagde volumes |<2 minuten |<ul><li>Het volume wordt beschikbaar gesteld binnen 2 minuten na een herstel- of kloonbewerking, ongeacht de volumegrootte.</li><li>De volumeprestaties kunnen in eerste instantie trager zijn dan normaal, omdat de meeste gegevens en metagegevens zich nog steeds in de cloud bevinden. De prestaties kunnen toenemen naarmate gegevens van de cloud naar het StorSimple-apparaat stromen.</li><li>De totale tijd om metagegevens te downloaden is afhankelijk van de toegewezen volumegrootte. Metagegevens worden automatisch op de achtergrond in het apparaat gebracht met een snelheid van 5 minuten per TB aan toegewezen volumegegevens. Dit tarief kan worden beïnvloed door internetbandbreedte naar de cloud.</li><li>De herstel- of kloonbewerking is voltooid wanneer alle metagegevens op het apparaat staan.</li><li>Back-upbewerkingen kunnen pas worden uitgevoerd als de herstel- of kloonbewerking volledig is voltooid. |
| Hersteltijd herstellen voor lokaal vastgemaakte volumes |<2 minuten |<ul><li>Het volume wordt beschikbaar gesteld binnen 2 minuten na de herstelbewerking, ongeacht de grootte van het volume.</li><li>De volumeprestaties kunnen in eerste instantie trager zijn dan normaal, omdat de meeste gegevens en metagegevens zich nog steeds in de cloud bevinden. De prestaties kunnen toenemen naarmate gegevens van de cloud naar het StorSimple-apparaat stromen.</li><li>De totale tijd om metagegevens te downloaden is afhankelijk van de toegewezen volumegrootte. Metagegevens worden automatisch op de achtergrond in het apparaat gebracht met een snelheid van 5 minuten per TB aan toegewezen volumegegevens. Dit tarief kan worden beïnvloed door internetbandbreedte naar de cloud.</li><li>In tegenstelling tot gelaagde volumes worden de volumegegevens ook lokaal gedownload op het apparaat als er lokaal vastgemaakte volumes zijn. De herstelbewerking is voltooid wanneer alle volumegegevens naar het apparaat zijn gebracht.</li><li>De herstelbewerkingen kunnen lang zijn en de totale tijd om het herstel te voltooien, is afhankelijk van de grootte van het ingerichte lokale volume, uw internetbandbreedte en de bestaande gegevens op het apparaat. Back-upbewerkingen op het lokaal vastgemaakte volume zijn toegestaan terwijl de herstelbewerking wordt uitgevoerd. |
| Beschikbaarheid voor thin-restore |Laatste failover | |
| Maximale lees-/schrijfdoorvoer van de client, wanneer deze wordt bediend vanuit de SSD-laag* |920/720 MB/sec met één 10-gigabit Ethernet-netwerkinterface |Tot twee keer toe met MPIO en twee netwerkinterfaces. |
| Maximale lees-/schrijfdoorvoer van de client, wanneer deze wordt bediend vanaf de HDD-laag* |120/250 MB/sec | |
| Maximale lees-/schrijfdoorvoer van de client, wanneer deze wordt bediend vanuit de cloudlaag* |11/41 MB/sec |Leesdoorvoer is afhankelijk van clients die voldoende I/O-wachtrijdiepte genereren en behouden. |

&#42;Maximale doorvoer per I/O-type werd gemeten met 100 procent gelezen en 100 procent schrijfscenario's. De werkelijke doorvoer kan lager zijn en is afhankelijk van de I/O-mix en netwerkomstandigheden.

