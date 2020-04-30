---
title: Backup en Restore – grootschalige (Citus)-Azure Database for PostgreSQL
description: Gegevens beveiligen tegen onbedoelde beschadiging of verwijdering
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: bcc94b62812f1668bf8c5e5abb268fddf3da1fa5
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515441"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Back-ups maken en herstellen in Azure Database for PostgreSQL-grootschalige (Citus)

Azure Database for PostgreSQL – grootschalige (Citus) maakt automatisch back-ups van elk knoop punt en slaat ze op in lokaal redundante opslag. Back-ups kunnen worden gebruikt om uw grootschalige-cluster (Citus) naar een opgegeven tijd te herstellen. Backup en Restore zijn een essentieel onderdeel van een strategie voor bedrijfs continuïteit omdat ze uw gegevens beschermen tegen onbedoelde beschadiging of verwijdering.

## <a name="backups"></a>Back-ups

Azure Database for PostgreSQL een moment opname maken van back-ups van gegevens bestanden en het transactie logboek van de data base. Met de back-ups kunt u een server herstellen naar elk gewenst moment binnen de Bewaar periode. (De Bewaar periode is momenteel 35 dagen voor alle clusters.) Alle back-ups worden versleuteld met AES 256-bits versleuteling.

In azure-regio's die beschikbaarheids zones ondersteunen, worden back-upmomentopnamen opgeslagen in drie beschikbaarheids zones. Zolang er ten minste één beschikbaarheids zone online is, is het grootschalige-cluster (Citus) te herstorbaar.

Back-upbestanden kunnen niet worden geëxporteerd. Ze mogen alleen worden gebruikt voor herstel bewerkingen in Azure Database for PostgreSQL.

### <a name="backup-storage-cost"></a>Kosten voor back-upopslag

Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)voor Azure database for PostgreSQL-grootschalige (Citus) voor actuele prijzen voor back-upopslag.

## <a name="restore"></a>Herstellen

In Azure Database for PostgreSQL maakt het herstellen van een grootschalige-cluster (Citus) een nieuw cluster op basis van de back-ups van de oorspronkelijke knoop punten.

> [!IMPORTANT]
> Verwijderde grootschalige-clusters (Citus) kunnen niet worden hersteld. Als u het cluster verwijdert, worden alle knoop punten die deel uitmaken van het cluster, verwijderd en kunnen ze niet worden hersteld. Voor het beveiligen van cluster bronnen, na implementatie, van onbedoeld verwijderen of onverwachte wijzigingen, kunnen beheerders gebruikmaken van [beheer vergrendelingen](/azure/azure-resource-manager/management/lock-resources).

### <a name="point-in-time-restore-pitr"></a>Herstel naar een bepaald tijdstip (PITR)

U kunt in de afgelopen 35 dagen een cluster herstellen naar elk gewenst moment.
Herstel naar een bepaald tijdstip is handig in meerdere scenario's. Wanneer een gebruiker bijvoorbeeld per ongeluk gegevens verwijdert, een belang rijke tabel of data base weglaat of als een toepassing per ongeluk goede gegevens overschrijft met ongeldige gegevens.

Het herstel proces maakt een nieuw cluster in dezelfde Azure-regio, hetzelfde abonnement en dezelfde resource groep als het origineel. Het cluster heeft de oorspronkelijke configuratie: hetzelfde aantal knoop punten, het aantal vCores, de opslag grootte, de gebruikers rollen, de PostgreSQL-versie en de versie van de Citus-extensie.

Firewall instellingen en PostgreSQL-server parameters blijven niet behouden van de oorspronkelijke server groep. deze worden ingesteld op de standaard waarden. De firewall voor komt dat alle verbindingen. U moet deze instellingen na het herstellen hand matig aanpassen.

> [!IMPORTANT]
> U moet een ondersteunings aanvraag openen om herstel naar een bepaald tijdstip van uw grootschalige-cluster (Citus) uit te voeren.

### <a name="post-restore-tasks"></a>Post-restore tasks

Na een herstel na een van beide herstel mechanismen moet u het volgende doen om uw gebruikers en toepassingen back-ups te maken en uit te voeren:

* Als de nieuwe server de oorspronkelijke server moet vervangen, kunt u clients en client toepassingen omleiden naar de nieuwe server
* Zorg ervoor dat de juiste firewall-en VNet-regels op server niveau aanwezig zijn voor gebruikers om verbinding te maken. Deze regels worden niet gekopieerd van de oorspronkelijke server groep.
* Pas de para meters van de PostgreSQL-server zo nodig aan. De para meters worden niet gekopieerd van de oorspronkelijke server groep.
* Zorg ervoor dat de juiste aanmeldingen en machtigingen op database niveau aanwezig zijn
* Waarschuwingen configureren, indien van toepassing

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure-beschikbaarheids zones](/azure/availability-zones/az-overview).
* Stel [voorgestelde waarschuwingen](/azure/postgresql/howto-hyperscale-alert-on-metric#suggested-alerts) in voor grootschalige (Citus)-Server groepen.
