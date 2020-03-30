---
title: Beheer opgeslagen procedures - Azure Database voor MySQL
description: Ontdek welke opgeslagen procedures in Azure Database voor MySQL nuttig zijn om u te helpen bij het configureren van gegevens-in-replicatie, het instellen van de tijdzone en het doden van query's.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 6a3fa40eaae174d3616fd0318f81576b7c59eac7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067704"
---
# <a name="azure-database-for-mysql-management-stored-procedures"></a>Azure Database voor opgeslagen procedures voor MySQL-beheer

Opgeslagen procedures zijn beschikbaar in Azure Database voor MySQL-servers om uw MySQL-server te beheren. Dit omvat het beheren van de verbindingen, query's en het instellen van gegevens-in-replicatie van uw server.  

## <a name="data-in-replication-stored-procedures"></a>Opgeslagen procedures voor replicatie in gegevens

Met replicatie van inkomende gegevens kunt u gegevens synchroniseren die afkomstig zijn van een MySQL-server die wordt uitgevoerd on-premises, in virtuele machines (VM's) of in databaseservices gehost door andere cloudproviders in de Azure Database for MySQL-service.

De volgende opgeslagen procedures worden gebruikt om Gegevens-in-replicatie tussen een stramien en replica in te stellen of te verwijderen.

|**Naam opgeslagen procedure**|**Invoerparameters**|**Uitvoerparameters**|**Opmerking voor gebruik**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N.v.t.|Als u gegevens wilt overbrengen met de SSL-modus, geeft u in de context van het CA-certificaat door naar de parameter master_ssl_ca. </br><br>Als u gegevens zonder SSL wilt overbrengen, geeft u een lege tekenreeks door in de parameter master_ssl_ca.|
|*mysql.az_replication _start*|N.v.t.|N.v.t.|Hiermee wordt de replicatie gestart.|
|*mysql.az_replication _stop*|N.v.t.|N.v.t.|Stopt replicatie.|
|*mysql.az_replication _remove_master*|N.v.t.|N.v.t.|Hiermee verwijdert u de replicatierelatie tussen het stramien en de replica.|
|*mysql.az_replication_skip_counter*|N.v.t.|N.v.t.|Hiermee slaat u één replicatiefout over.|

Als u Gegevens-in-replicatie wilt instellen tussen een stramien en een replica in Azure Database voor MySQL, raadpleegt u [hoe u Gegevens-in-replicatie configureert.](howto-data-in-replication.md)

## <a name="other-stored-procedures"></a>Andere opgeslagen procedures

De volgende opgeslagen procedures zijn beschikbaar in Azure Database voor MySQL om uw server te beheren.

|**Naam opgeslagen procedure**|**Invoerparameters**|**Uitvoerparameters**|**Opmerking voor gebruik**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N.v.t.|Gelijk [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) aan commando. Hiermee beëindigt u de verbinding die is gekoppeld aan de verstrekte processlist_id na beëindiging van een instructie die de verbinding uitvoert.|
|*mysql.az_kill_query*|processlist_id|N.v.t.|Gelijk [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) aan commando. Beëindigt de instructie die de verbinding momenteel uitvoert. Laat de verbinding zelf in leven.|
|*mysql.az_load_timezone*|N.v.t.|N.v.t.|Laadt tijdzonetabellen op `time_zone` zodat de parameter kan worden ingesteld op benoemde waarden (bijvoorbeeld. "VS/Pacific").|

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het instellen van [gegevens-in-replicatie](howto-data-in-replication.md)
- Meer informatie over het gebruik van de [tijdzonetabellen](howto-server-parameters.md#working-with-the-time-zone-parameter)
