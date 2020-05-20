---
title: Ondersteunings matrix voor back-up van Azure-bestands share
description: Hierin wordt een overzicht gegeven van de ondersteunings instellingen en beperkingen bij het maken van back-ups van Azure-bestands shares.
ms.topic: conceptual
ms.date: 5/07/2020
ms.openlocfilehash: 42578cc83ef193801fa700ec7d136385411e5f79
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684619"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Ondersteunings matrix voor back-up van Azure-bestands share

U kunt de [Azure backup-service](https://docs.microsoft.com/azure/backup/backup-overview) gebruiken om een back-up te maken van Azure-bestands shares. Dit artikel bevat een overzicht van de ondersteunings instellingen bij het maken van back-ups van Azure-bestands shares met Azure Backup.

## <a name="supported-geos"></a>Ondersteunde geografische gebieden

Back-ups voor Azure-bestands shares zijn beschikbaar in de volgende geografische gebieden:

**Ga-gebieden**:<br>
Australië-Zuid-Oost (ASE), Canada-centraal (CNC), VS-West-Centraal (WCUS), Zuid-Centraal VS (SCUS), VS-West 2 (WUS 2), India Zuid (invoeg toepassingen), Noord-Centraal VS (NCUS), Japan-Oost (JPE), Brazilië-zuid (BRS), Zuid-Azië-oost (SEA), Zwitserland-west (SZW), UAE-centraal (UAC), Noor wegen Oost (NAANBEVOLEN), India-West (INW), Australië-centraal (ACL), Korea-centraal (KRC), Japan-West (JPW), Zuid-Afrika-noord (SAN) , UK-zuid (UKS), UK-west (UKW), Korea-zuid (KRS), Europa-noord (NE), Duitsland-noord (GN), Noor wegen West (NWW), Zuid-Afrika-west (zaag), Zwitserland-noord (SZN), Duitsland-west-centraal (GWC), UAE-noord (UAN), Frankrijk-centraal (FRC), India Central (INC), Canada-oost (CNE), Azië-oost (EA), Australië-oost (AE), Central US (CUS), VS-West (WUS), US Gov-Arizona (UGA), US Gov-Texas (UGT) , US Gov-Virginia (UGV), US DoD-centraal (UDC), US DoD-oost (USIEF)

**Ondersteunde regio's (als onderdeel van de preview-versie) maar nog geen ga**:<br>
VS-Oost (EUS), VS-Oost 2 (EUS2), Europa-west (WE)

## <a name="supported-storage-accounts"></a>Ondersteunde opslagaccounts

| Details van opslag account | Ondersteuning                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Soort account            | Azure Backup biedt ondersteuning voor Azure-bestands shares die beschikbaar zijn in het algemeen gebruik van v1, algemeen gebruik v2 en opslag accounts voor bestands opslag. |
| Prestaties              | Azure Backup ondersteunt bestands shares in zowel standaard-als Premium Storage-accounts |
| Replicatie              | Azure-bestands shares in opslag accounts met elk replicatie type worden ondersteund |

## <a name="supported-file-shares"></a>Ondersteunde bestands shares

| Bestands share type                                   | Ondersteuning   |
| -------------------------------------------------- | --------- |
| Standard                                           | Ondersteund |
| Groot                                              | Ondersteund |
| Premium                                            | Ondersteund |
| Bestands shares die zijn verbonden met de Azure File Sync-Service | Ondersteund |

## <a name="protection-limits"></a>Beveiligings limieten

| Instelling                                                      | Limiet |
| ------------------------------------------------------------ | ----- |
| Maximum aantal bestands shares dat per dag per kluis kan worden beveiligd | 200   |
| Maximum aantal opslag accounts dat per kluis per dag kan worden geregistreerd | 50    |

## <a name="backup-limits"></a>Back-uplimieten

| Instelling                                      | Limiet |
| -------------------------------------------- | ----- |
| Maximum aantal back-ups op aanvraag per dag | 4     |
| Maximum aantal geplande back-ups per dag | 1     |

## <a name="restore-limits"></a>Limieten voor herstellen

| Instelling                                                      | Limiet   |
| ------------------------------------------------------------ | ------- |
| Maximum aantal herstel bewerkingen per dag                           | 10      |
| Maximum aantal bestanden per herstel bewerking                         | 10      |
| Maximale aanbevolen grootte voor herstellen per herstel voor grote bestands shares | 15 TiB |

## <a name="retention-limits"></a>Bewaarlimieten

| Instelling                                                      | Limiet    |
| ------------------------------------------------------------ | -------- |
| Maximum aantal herstel punten per bestands share op elk gewenst moment | 200      |
| Maximale Bewaar periode van het herstel punt dat is gemaakt met back-ups op aanvraag | tien jaar |
| Maximale Bewaar termijn van dagelijkse herstel punten (moment opnamen) per bestands share| 200 dagen |
| Maximale retentie van wekelijkse herstel punten (moment opnamen) per bestands share | 200 weken |
| Maximale retentie van maandelijkse herstel punten (moment opnamen) per bestands share | 120 maanden |
| Maximale retentie van jaarlijkse herstel punten (moment opnamen) per bestands share | tien jaar |

## <a name="supported-restore-methods"></a>Ondersteunde herstel methoden

| Methode Restore     | Details                                                      |
| ------------------ | ------------------------------------------------------------ |
| Volledige share herstellen | U kunt de volledige bestands share herstellen naar de oorspronkelijke of een alternatieve locatie |
| Herstellen op item niveau | U kunt afzonderlijke bestanden en mappen herstellen naar de oorspronkelijke of een alternatieve locatie |

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van back-ups van Azure-bestands shares](backup-afs.md)
* Meer informatie over het [herstellen van Azure-bestands shares](restore-afs.md)
* Meer informatie over het [beheren van back-ups van Azure-bestands shares](manage-afs-backup.md)
