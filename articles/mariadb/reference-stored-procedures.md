---
title: Opgeslagen procedures voor Azure Database for MariaDB beheer
description: Informatie over welke opgeslagen procedures in Azure Database for MySQL nuttig zijn om u te helpen bij het configureren van replicatie van gegevens in, het instellen van de tijd zone en het beëindigen van query's.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 0a33edeac735502964427ddc3b05076fb9fac969
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973470"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Opgeslagen procedures voor Azure Database for MariaDB beheer

Opgeslagen procedures zijn beschikbaar op Azure Database for MariaDB servers om uw MariaDB-server te beheren. Dit omvat het beheren van de verbindingen van uw server, het uitvoeren van query's en het instellen van Replicatie van inkomende gegevens.  

## <a name="data-in-replication-stored-procedures"></a>Opgeslagen procedures Replicatie van inkomende gegevens

Met replicatie van inkomende gegevens kunt u gegevens synchroniseren die afkomstig zijn van een MariaDB-server die wordt uitgevoerd on-premises, in virtuele machines (VM's) of in databaseservices gehost door andere cloudproviders in de Azure Database for MariaDB-service.

De volgende opgeslagen procedures worden gebruikt om Replicatie van inkomende gegevens in te stellen of te verwijderen tussen een hoofd database en een replica.

|**Naam van opgeslagen procedure**|**Invoerparameters**|**Uitvoer parameters**|**Opmerking over gebruik**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/A|Als u gegevens wilt overdragen met de SSL-modus, geeft u de context van het CA-certificaat door in de para meter master_ssl_ca. </br><br>Als u gegevens zonder SSL wilt overdragen, geeft u een lege teken reeks door in de para meter master_ssl_ca.|
|*mysql.az_replication _start*|N/A|N/A|Replicatie wordt gestart.|
|*mysql.az_replication _stop*|N/A|N/A|De replicatie wordt gestopt.|
|*mysql.az_replication _remove_master*|N/A|N/A|Hiermee verwijdert u de replicatie relatie tussen het hoofd en de replica.|
|*mysql.az_replication_skip_counter*|N/A|N/A|Hiermee wordt één replicatie fout overs Laan.|

Zie [replicatie van inkomende gegevens configureren voor informatie over het](howto-data-in-replication.md)instellen van replicatie van inkomende gegevens tussen een Master en een replica in azure database for MariaDB.

## <a name="other-stored-procedures"></a>Andere opgeslagen procedures

De volgende opgeslagen procedures zijn beschikbaar in Azure Database for MariaDB voor het beheren van uw server.

|**Naam van opgeslagen procedure**|**Invoerparameters**|**Uitvoer parameters**|**Opmerking over gebruik**|
|-----|-----|-----|-----|
|*MySQL. AZ _kill*|processlist_id|N/A|Gelijk aan de opdracht [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) . De verbinding die is gekoppeld aan de geleverde processlist_id wordt beëindigd na het beëindigen van een instructie die de verbinding uitvoert.|
|*MySQL. AZ _kill_query*|processlist_id|N/A|Gelijk aan de opdracht [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) . Hiermee wordt de instructie beëindigd die momenteel wordt uitgevoerd door de verbinding. De verbinding zelf blijft actief.|
|*MySQL. AZ _load_timezone*|N/A|N/A|Laadt tijd zone tabellen zodat de para meter `time_zone` kan worden ingesteld op benoemde waarden (bijvoorbeeld "VS/Pacific").|

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het instellen van [replicatie van inkomende gegevens](howto-data-in-replication.md)
- Meer informatie over het gebruik van de [tabellen met tijd zones](howto-server-parameters.md#working-with-the-time-zone-parameter)