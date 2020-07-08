---
title: Ondersteunings matrix voor back-up van Azure-bestands share
description: Hierin wordt een overzicht gegeven van de ondersteunings instellingen en beperkingen bij het maken van back-ups van Azure-bestands shares.
ms.topic: conceptual
ms.date: 5/07/2020
ms.custom: references_regions
ms.openlocfilehash: 5670f4702a8e8d199b9762d87793a053cf6e53b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85806868"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Ondersteunings matrix voor back-up van Azure-bestands share

U kunt de [Azure backup-service](https://docs.microsoft.com/azure/backup/backup-overview) gebruiken om een back-up te maken van Azure-bestands shares. Dit artikel bevat een overzicht van de ondersteunings instellingen bij het maken van back-ups van Azure-bestands shares met Azure Backup.

## <a name="supported-regions"></a>Ondersteunde regio’s

### <a name="ga-regions-for-azure-file-shares-backup"></a>GA-regio's voor back-up van Azure-bestands shares

Back-ups van Azure-bestands shares zijn beschikbaar in alle regio's, **met uitzonde ring** van: Duitsland-centraal (soeverein), Duitsland-Noordoost (soeverein), China-oost, China-Oost 2, China-noord, China-noord 2 US gov-Iowa

### <a name="supported-regions-for-accidental-delete-protection"></a>Ondersteunde regio's voor onbedoeld verwijderen van beveiliging

West-Centraal VS, Australië-Zuid-Oost, Canada-centraal

## <a name="supported-storage-accounts"></a>Ondersteunde opslagaccounts

| Details van opslag account | Ondersteuning                                                      |
| ------------------------ | ------------------------------------------------------------ |
| Soort account            | Azure Backup biedt ondersteuning voor Azure-bestands shares die beschikbaar zijn in het algemeen gebruik van v1, algemeen gebruik v2 en opslag accounts voor bestands opslag. |
| Prestaties              | Azure Backup ondersteunt bestands shares in zowel standaard-als Premium Storage-accounts |
| Replicatie              | Azure-bestands shares in opslag accounts met elk replicatie type worden ondersteund |
| Firewall ingeschakeld         | Azure-bestands shares in opslag accounts met firewall regels waarmee Microsoft Azure Services toegang kunnen krijgen tot het opslag account, worden ondersteund|

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
| Maximum aantal bestands shares per dag dat per kluis kan worden beveiligd| 200   |
| Maximum aantal opslag accounts dat per kluis per dag kan worden geregistreerd | 50    |
| Maximum aantal bestands shares dat per kluis kan worden beveiligd | 2000   |
| Maximum aantal opslag accounts dat per kluis kan worden geregistreerd | 200   |

## <a name="backup-limits"></a>Back-uplimieten

| Instelling                                      | Limiet |
| -------------------------------------------- | ----- |
| Maximum aantal back-ups op aanvraag per dag | 10   |
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
