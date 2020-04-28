---
title: Ondersteunings matrix voor back-up van Azure-bestands share
description: Hierin wordt een overzicht gegeven van de ondersteunings instellingen en beperkingen bij het maken van back-ups van Azure-bestands shares.
ms.topic: conceptual
ms.date: 1/26/2020
ms.openlocfilehash: 03190c6b83fceec88442583a57ff46756cfe6221
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195686"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Ondersteunings matrix voor back-up van Azure-bestands share

U kunt de [Azure backup-service](https://docs.microsoft.com/azure/backup/backup-overview) gebruiken om een back-up te maken van Azure-bestands shares. Dit artikel bevat een overzicht van de ondersteunings instellingen bij het maken van back-ups van Azure-bestands shares met Azure Backup.

## <a name="supported-geos"></a>Ondersteunde geografische gebieden

Back-ups voor Azure-bestands shares zijn beschikbaar in de volgende geografische gebieden:

| GA-regio's | Ondersteunde regio's, maar geen GA                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Australië-Zuid-Oost (ASE), Canada-centraal (CNC), VS-West-Centraal (WCUS), VS-West 2 (WUS 2), India Zuid (invoeg toepassingen), Noord-Centraal VS (NCUS), Japan-Oost (JPE), Brazilië-zuid (BRS), Zuid Azië-oost (SEA)                                                     |Australië-oost (AE), Canada-oost (CE), Azië-oost (EA), VS-Oost (EUS), VS-Oost 2 (EUS2), Japan-West (JPW), India centraal (INC.), Korea-centraal (KRC), Korea-zuid (KRS), Europa-noord (NE), Zuid-Centraal VS (SCUS), UK-zuid (UKS), UK-west (UKW), Europa-west (WE), VS-West (WUS), US Gov-Arizona (UGA), US gov-Texas (UGT), US Gov-Virginia (UGV), Australië-centraal (ACL) , India West (INW), Zuid-Afrika-noord (SAN), UAE-noord (UAN), Frankrijk-centraal (FRC), Duitsland-noord (GN), Duitsland-west-centraal (GWC), Zuid-Afrika-west (zaag), UAE-centraal (UAC), Noor wegen Oost (NAANBEVOLEN), Noor wegen West (NWW), Zwitserland-noord (SZN), VS-Midden (CUS)           |

## <a name="supported-storage-accounts"></a>Ondersteunde opslagaccounts

| Details van opslag account | Ondersteuning                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Soort account            | Azure Backup biedt ondersteuning voor Azure-bestands shares die beschikbaar zijn in de algemene versie van v1, algemeen gebruik v2 en bestands share type opslag accounts |
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
