---
title: Gegevens repliceren naar Azure Database for MySQL
description: Meer informatie over het gebruik van replicatie van gegevens in een externe server naar de Azure Database for MySQL-service.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: b501a1f1ea54aff5617932dc5085d6d19f86976c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970356"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Gegevens repliceren naar Azure Database for MySQL

Met Replicatie van inkomende gegevens kunt u gegevens van een externe MySQL-server synchroniseren met de Azure Database for MySQL-service. De externe server kan on-premises, in virtuele machines of een database service worden gehost door andere cloud providers. Replicatie van binnenkomende gegevens is gebaseerd op het binaire logbestand (binlog) met replicatie op basis van positie eigen aan MySQL. Meer informatie over binlog-replicatie vindt u in het [overzicht van MySQL binlog-replicatie](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Wanneer moet ik Replicatie van inkomende gegevens gebruiken?
De belangrijkste scenario's voor het gebruik van Replicatie van inkomende gegevens zijn:

- **Gegevens synchronisatie hybride:** Met Replicatie van inkomende gegevens kunt u gegevens gesynchroniseerd blijven tussen uw on-premises servers en Azure Database for MySQL. Deze synchronisatie is handig voor het maken van hybride toepassingen. Deze methode is handig wanneer u een bestaande lokale database server hebt, maar de gegevens naar een regio dichter bij eind gebruikers wilt verplaatsen.
- **Synchronisatie met meerdere Clouds:** Gebruik Replicatie van inkomende gegevens voor complexe cloud oplossingen om gegevens te synchroniseren tussen Azure Database for MySQL en verschillende cloud providers, met inbegrip van virtuele machines en database services die worden gehost in die Clouds.
 
Gebruik de [Azure database Migration service](https://azure.microsoft.com/services/database-migration/)(DMS) voor migratie scenario's.

## <a name="limitations-and-considerations"></a>Beperkingen en overwegingen

### <a name="data-not-replicated"></a>Gegevens niet gerepliceerd
De [*MySQL-systeem database*](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) op de hoofd server wordt niet gerepliceerd. Wijzigingen van accounts en machtigingen op de hoofd server worden niet gerepliceerd. Als u een account op de master server maakt en dit account toegang heeft tot de replica server, moet u het account hand matig op de replica server maken. Zie de [MySQL-hand leiding](https://dev.mysql.com/doc/refman/5.7/en/system-database.html)voor meer informatie over de tabellen die zijn opgenomen in de systeem database.

### <a name="requirements"></a>Vereisten
- De versie van de hoofd server moet ten minste MySQL-versie 5,6 zijn. 
- De versies van de hoofd-en replica server moeten hetzelfde zijn. Beide moeten bijvoorbeeld MySQL-versie 5,6 zijn of beide moeten MySQL-versie 5,7 zijn.
- Elke tabel moet een primaire sleutel hebben.
- De MySQL InnoDB-engine moet worden gebruikt voor de hoofd server.
- De gebruiker moet machtigingen hebben voor het configureren van binaire logboek registratie en het maken van nieuwe gebruikers op de hoofd server.
- Als SSL is ingeschakeld op de master server, moet u ervoor zorgen dat het SSL-CA-certificaat dat is opgegeven voor het domein, is opgenomen in de opgeslagen procedure `mysql.az_replication_change_master`. Raadpleeg de volgende [voor beelden](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) en de para meter `master_ssl_ca`.
- Zorg ervoor dat het IP-adres van de hoofdserver is toegevoegd aan de firewallregels van de Azure Database for MySQL-replicaserver. Firewallregels bijwerken met de [Azure-portal](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) of [Azure CLI](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli).
- Zorg ervoor dat de computer waarop de hoofdserver wordt gehost zowel binnenkomend als uitgaand verkeer op poort 3306 toestaat.
- Zorg ervoor dat de hoofd server een **openbaar IP-adres**heeft, dat de DNS openbaar toegankelijk is of een Fully QUALIFIED domain name (FQDN) heeft.

### <a name="other"></a>Overige
- Replicatie van gegevens wordt alleen ondersteund in de Algemeen en de prijs categorieën die zijn geoptimaliseerd voor geheugen.
- Algemene trans actie-id's (GTID) worden niet ondersteund.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [instellen van gegevens replicatie](howto-data-in-replication.md)
- Meer informatie over [repliceren in azure met replica's lezen](concepts-read-replicas.md)
- Meer informatie over het [migreren van gegevens met minimale downtime met behulp van DMS](howto-migrate-online.md)