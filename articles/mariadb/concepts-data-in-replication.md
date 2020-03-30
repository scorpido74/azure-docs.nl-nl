---
title: Gegevens-in-replicatie - Azure-database voor MariaDB
description: Meer informatie over het gebruik van gegevens-in-replicatie om te synchroniseren vanaf een externe server naar de Azure Database for MariaDB-service.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 1fbcc1fb27d5e6df4641f79c0d634580f74000b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532057"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Gegevens repliceren in Azure Database voor MariaDB

Met replicatie van inkomende gegevens kunt u gegevens synchroniseren die afkomstig zijn van een MariaDB-server die wordt uitgevoerd on-premises, in virtuele machines (VM's) of in databaseservices gehost door andere cloudproviders in de Azure Database for MariaDB-service. Replicatie van binnenkomende gegevens is gebaseerd op het binaire logbestand (binlog) met replicatie op basis van positie eigen aan MariaDB. Zie het overzicht van de [binlogreplicatie](https://mariadb.com/kb/en/library/replication-overview/)voor meer informatie over binlog-replicatie.

## <a name="when-to-use-data-in-replication"></a>Wanneer gegevens in replicatie gebruiken
De belangrijkste scenario's die u moet overwegen om gegevens in replicatie te gebruiken, zijn:

- **Hybride gegevenssynchronisatie:** Met Data-in-replicatie u gegevens gesynchroniseerd houden tussen uw on-premises servers en Azure Database voor MariaDB. Deze synchronisatie is handig voor het maken van hybride toepassingen. Deze methode is aantrekkelijk wanneer u een bestaande lokale databaseserver hebt, maar de gegevens wilt verplaatsen naar een regio dichter bij eindgebruikers.
- **Multi-Cloud synchronisatie:** Gebruik Gegevens-in-replicatie voor complexe cloudoplossingen om gegevens te synchroniseren tussen Azure Database voor MariaDB en verschillende cloudproviders, waaronder virtuele machines en databaseservices die in die clouds worden gehost.

## <a name="limitations-and-considerations"></a>Beperkingen en overwegingen

### <a name="data-not-replicated"></a>Gegevens die niet worden gerepliceerd
De [*mysql-systeemdatabase*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) op de hoofdserver wordt niet gerepliceerd. Wijzigingen in accounts en machtigingen op de hoofdserver worden niet gerepliceerd. Als u een account maakt op de hoofdserver en dit account toegang moet krijgen tot de replicaserver, maakt u handmatig hetzelfde account aan de kant van de replicaserver. Zie de [MariaDB-documentatie](https://mariadb.com/kb/en/library/the-mysql-database-tables/)om te begrijpen welke tabellen in de systeemdatabase zijn opgenomen.

### <a name="requirements"></a>Vereisten
- De master server versie moet ten minste MariaDB versie 10.2.
- De versies van de hoofd- en replicaserver moeten hetzelfde zijn. Beide moeten bijvoorbeeld MariaDB-versie 10.2 zijn.
- Elke tabel moet een primaire sleutel hebben.
- Master server moet de InnoDB-engine gebruiken.
- De gebruiker moet machtigingen hebben om binaire logboekregistratie te configureren en nieuwe gebruikers op de hoofdserver te maken.
- Als ssl is ingeschakeld voor de hoofdserver, moet u ervoor zorgen `mariadb.az_replication_change_master` dat het SSL CA-certificaat voor het domein is opgenomen in de opgeslagen procedure. Raadpleeg de volgende [voorbeelden](https://docs.microsoft.com/azure/mariadb/howto-data-in-replication#link-the-master-and-replica-servers-to-start-data-in-replication) `master_ssl_ca` en de parameter.
- Zorg ervoor dat het IP-adres van de hoofdserver is toegevoegd aan de firewallregels van de Azure Database for MariaDB-replicaserver. Firewallregels bijwerken met de [Azure-portal](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-portal) of [Azure CLI](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-cli).
- Zorg ervoor dat de computer waarop de hoofdserver wordt gehost zowel binnenkomend als uitgaand verkeer op poort 3306 toestaat.
- Zorg ervoor dat de hoofdserver een **openbaar IP-adres**heeft, dat de DNS openbaar toegankelijk is of een volledig gekwalificeerde domeinnaam (FQDN) heeft.

### <a name="other"></a>Overige
- Gegevensreplicatie wordt alleen ondersteund in de prijsniveaus Voor algemeen gebruik en geheugengeoptimaliseerd.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [instellen van gegevens-in-replicatie](howto-data-in-replication.md).
