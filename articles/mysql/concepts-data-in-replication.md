---
title: Gegevens-in-replicatie - Azure Database voor MySQL
description: Meer informatie over het gebruik van gegevens-in-replicatie om te synchroniseren vanaf een externe server naar de Azure Database for MySQL-service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 20be34191355e6ade40e0f3b218818bfa5345a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533229"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Gegevens repliceren in Azure Database voor MySQL

Met data-in-replicatie u gegevens van een externe MySQL-server synchroniseren met de Azure Database for MySQL-service. De externe server kan on-premises zijn, in virtuele machines of een databaseservice die wordt gehost door andere cloudproviders. Replicatie van binnenkomende gegevens is gebaseerd op het binaire logbestand (binlog) met replicatie op basis van positie eigen aan MySQL. Zie het overzicht van [mySQL binlog-replicatie](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)voor meer informatie over binlog-replicatie. 

## <a name="when-to-use-data-in-replication"></a>Wanneer gegevens in replicatie gebruiken
De belangrijkste scenario's die u moet overwegen om gegevens in replicatie te gebruiken, zijn:

- **Hybride gegevenssynchronisatie:** Met Data-in-replicatie u gegevens gesynchroniseerd houden tussen uw on-premises servers en Azure Database voor MySQL. Deze synchronisatie is handig voor het maken van hybride toepassingen. Deze methode is aantrekkelijk wanneer u een bestaande lokale databaseserver hebt, maar de gegevens wilt verplaatsen naar een regio dichter bij eindgebruikers.
- **Multi-Cloud synchronisatie:** Gebruik Gegevens-in-replicatie voor complexe cloudoplossingen om gegevens te synchroniseren tussen Azure Database voor MySQL en verschillende cloudproviders, waaronder virtuele machines en databaseservices die in die clouds worden gehost.
 
Gebruik voor migratiescenario's de [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/)(DMS).

## <a name="limitations-and-considerations"></a>Beperkingen en overwegingen

### <a name="data-not-replicated"></a>Gegevens die niet worden gerepliceerd
De [*mysql-systeemdatabase*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) op de hoofdserver wordt niet gerepliceerd. Wijzigingen in accounts en machtigingen op de hoofdserver worden niet gerepliceerd. Als u een account maakt op de hoofdserver en dit account toegang moet krijgen tot de replicaserver, maakt u handmatig hetzelfde account aan de kant van de replicaserver. Zie de [MySQL-handleiding](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)om te begrijpen welke tabellen zich in de systeemdatabase bevinden.

### <a name="requirements"></a>Vereisten
- De master server versie moet ten minste MySQL versie 5.6. 
- De versies van de hoofd- en replicaserver moeten hetzelfde zijn. Beide moeten bijvoorbeeld MySQL-versie 5.6 zijn of beide moeten MySQL-versie 5.7 zijn.
- Elke tabel moet een primaire sleutel hebben.
- Master server moet de MySQL InnoDB-engine gebruiken.
- De gebruiker moet machtigingen hebben om binaire logboekregistratie te configureren en nieuwe gebruikers op de hoofdserver te maken.
- Als ssl is ingeschakeld voor de hoofdserver, moet u ervoor zorgen `mysql.az_replication_change_master` dat het SSL CA-certificaat voor het domein is opgenomen in de opgeslagen procedure. Raadpleeg de volgende [voorbeelden](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) `master_ssl_ca` en de parameter.
- Zorg ervoor dat het IP-adres van de hoofdserver is toegevoegd aan de firewallregels van de Azure Database for MySQL-replicaserver. Firewallregels bijwerken met de [Azure-portal](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) of [Azure CLI](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli).
- Zorg ervoor dat de computer waarop de hoofdserver wordt gehost zowel binnenkomend als uitgaand verkeer op poort 3306 toestaat.
- Zorg ervoor dat de hoofdserver een **openbaar IP-adres**heeft, dat de DNS openbaar toegankelijk is of een volledig gekwalificeerde domeinnaam (FQDN) heeft.

### <a name="other"></a>Overige
- Gegevensreplicatie wordt alleen ondersteund in de prijsniveaus Voor algemeen gebruik en geheugengeoptimaliseerd.
- Globale transactie-id's (GTID) worden niet ondersteund.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [instellen van gegevens-in-replicatie](howto-data-in-replication.md)
- Meer informatie over [repliceren in Azure met gelezen replica's](concepts-read-replicas.md)
- Meer informatie over het [migreren van gegevens met minimale downtime met DMS](howto-migrate-online.md)