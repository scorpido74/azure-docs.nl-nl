---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67176433"
---
| Limiet-id | Limiet | Opmerkingen |
| --- | --- | --- |
| Maximum aantal referenties voor het opslag account |64 | |
| Maximum aantal volume containers |64 | |
| Maximum aantal volumes |255 | |
| Maximum aantal planningen per band breedte sjabloon |168 |Een planning voor elk uur, elke dag van de week. |
| Maximale grootte van een gelaagd volume op fysieke apparaten |64 TB voor StorSimple 8100 en StorSimple 8600 |StorSimple 8100 en StorSimple 8600 zijn fysieke apparaten. |
| Maximale grootte van een gelaagd volume op virtuele apparaten in azure |30 TB voor StorSimple 8010 <br></br> 64 TB voor StorSimple 8020 |StorSimple 8010 en StorSimple 8020 zijn virtuele apparaten in azure die respectievelijk gebruikmaken van standaard opslag en Premium-opslag. |
| Maximale grootte van lokaal vastgemaakt volume op fysieke apparaten |9 TB voor StorSimple 8100 <br></br> 24 TB voor StorSimple 8600 |StorSimple 8100 en StorSimple 8600 zijn fysieke apparaten. |
| Maximum aantal iSCSI-verbindingen |512 | |
| Maximum aantal iSCSI-verbindingen van initia tors |512 | |
| Maximum aantal toegangs beheer records per apparaat |64 | |
| Maximum aantal volumes per back-upbeleid |24 | |
| Maximum aantal back-ups dat per back-upbeleid wordt bewaard |64 | |
| Maximum aantal planningen per back-upbeleid |10 | |
| Maximum aantal moment opnamen van elk type dat per volume kan worden bewaard |256 |Dit bedrag omvat lokale moment opnamen en Cloud momentopnamen. |
| Het maximum aantal moment opnamen dat aanwezig kan zijn op elk apparaat |10.000 | |
| Maximum aantal volumes dat parallel kan worden verwerkt voor back-up, herstel of kloon |16 |<ul><li>Als er meer dan 16 volumes zijn, worden ze opeenvolgend verwerkt omdat er verwerkings sleuven beschikbaar zijn.</li><li>Nieuwe back-ups van een gekloond of een hersteld gelaagd volume kunnen niet worden uitgevoerd totdat de bewerking is voltooid. Voor een lokaal volume zijn back-ups toegestaan nadat het volume online is.</li></ul> |
| Herstel tijd herstellen en klonen voor gelaagde volumes |<2 minuten |<ul><li>Het volume wordt binnen twee minuten na een herstel-of kloon bewerking beschikbaar gemaakt, ongeacht de grootte van het volume.</li><li>De prestaties van het volume zijn mogelijk langzamer dan normaal, omdat de meeste gegevens en meta gegevens zich nog steeds in de cloud bevinden. De prestaties kunnen toenemen als gegevens stromen van de Cloud naar het StorSimple-apparaat.</li><li>De totale tijd voor het downloaden van meta gegevens is afhankelijk van de grootte van het toegewezen volume. Meta gegevens worden op de achtergrond automatisch op het apparaat gebracht, met een snelheid van vijf minuten per TB aan toegewezen volume gegevens. Dit aantal kan worden beïnvloed door de Internet bandbreedte voor de Cloud.</li><li>De herstel-of kloon bewerking is voltooid wanneer alle meta gegevens zich op het apparaat bevinden.</li><li>Er kunnen geen back-upbewerkingen worden uitgevoerd tot het terugzetten of de kloon is voltooid. |
| Herstel tijd herstellen voor lokaal vastgemaakte volumes |<2 minuten |<ul><li>Het volume wordt beschikbaar gesteld binnen twee minuten nadat de herstel bewerking is uitgevoerd, ongeacht de grootte van het volume.</li><li>De prestaties van het volume zijn mogelijk langzamer dan normaal, omdat de meeste gegevens en meta gegevens zich nog steeds in de cloud bevinden. De prestaties kunnen toenemen als gegevens stromen van de Cloud naar het StorSimple-apparaat.</li><li>De totale tijd voor het downloaden van meta gegevens is afhankelijk van de grootte van het toegewezen volume. Meta gegevens worden op de achtergrond automatisch op het apparaat gebracht, met een snelheid van vijf minuten per TB aan toegewezen volume gegevens. Dit aantal kan worden beïnvloed door de Internet bandbreedte voor de Cloud.</li><li>In tegens telling tot gelaagde volumes geldt dat als er lokaal vastgemaakte volumes zijn, de volume gegevens ook lokaal op het apparaat worden gedownload. De herstel bewerking is voltooid wanneer alle volume gegevens naar het apparaat zijn overgebracht.</li><li>De herstel bewerkingen kunnen lang duren en de totale tijd voor het volt ooien van de herstel bewerking is afhankelijk van de grootte van het ingerichte lokale volume, uw Internet bandbreedte en de bestaande gegevens op het apparaat. Back-upbewerkingen op het lokaal vastgemaakte volume zijn toegestaan tijdens het terugzetten. |
| Thin-Restore-Beschik baarheid |Laatste failover | |
| Maximale door Voer voor lezen/schrijven van client, wanneer bediend vanuit de SSD-laag * |920/720 MB/sec. met één 10 Gigabit Ethernet-netwerk interface |Maxi maal twee keer met MPIO en twee netwerk interfaces. |
| Maximale door Voer voor lezen/schrijven van client, wanneer bediend vanuit de HDD-laag * |120/250 MB/sec. | |
| Maximale door Voer voor lezen/schrijven van client, wanneer deze wordt bediend vanuit de Cloud-laag * |11/41 MB/sec. |Lees doorvoer is afhankelijk van clients die voldoende I/O-wachtrij diepte genereren en onderhouden. |

&#42;maximale door Voer per I/O-type werd gemeten met 100 procent Lees-en 100 procent schrijf scenario's. De werkelijke door Voer is mogelijk lager en is afhankelijk van I/O-mix en netwerk omstandigheden.

