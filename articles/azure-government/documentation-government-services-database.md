---
title: Azure Government data bases | Microsoft Docs
description: Dit biedt een vergelijking van de functies en richt lijnen voor het ontwikkelen van toepassingen voor Azure Government
services: azure-government
cloud: gov
documentationcenter: ''
author: zakramer
manager: liki
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 03/06/2019
ms.author: zakramer
ms.openlocfilehash: cbf3d59bafa5b11be0fa037487dc64fdb9075619
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355865"
---
# <a name="azure-government-databases"></a>Azure Government-data bases
## <a name="sql-database"></a>SQL Database
Zie voor meer informatie de<a href="https://msdn.microsoft.com/library/bb510589.aspx"> micro soft-Security Center voor SQL database-engine</a> en [Azure SQL database documentatie](../sql-database/index.yml) voor meer uitleg over configuratie van de zicht baarheid van meta gegevens en aanbevolen procedures voor beveiliging.

### <a name="variations"></a>Verschillen
SQL V12 data base is algemeen beschikbaar in Azure Government.

Het adres voor SQL Azure servers in Azure Government wijkt af van het volgende:

| Servicetype | Open bare Azure | Azure Government |
| --- | --- | --- |
| SQL Database |*. database.windows.net |*. database.usgovcloudapi.net |

### <a name="considerations"></a>Overwegingen
De volgende informatie bevat de Azure Government grens voor Azure SQL:

| Gereguleerde/beheerde gegevens toegestaan | Gereguleerde/beheerde gegevens niet toegestaan |
| --- | --- |
| Alle opgeslagen en verwerkte gegevens in Microsoft Azure SQL kunnen Azure Government-gereglementeerde gegevens bevatten. Gebruik database hulpprogramma's voor gegevens overdracht van door Azure Government gereglementeerde gegevens. |Azure SQL-meta gegevens mogen geen door export beheerde gegevens bevatten. Deze meta gegevens bevatten alle configuratie gegevens die zijn ingevoerd bij het maken en onderhouden van uw opslag product.  Voer geen gereguleerde/beheerde gegevens in de volgende velden in: database naam, naam van abonnement, resource groepen, Server naam, aanmelden bij Server beheerder, implementatie namen, resource namen, resource Tags |

## <a name="sql-data-warehouse"></a>SQL Data Warehouse
Zie [Azure SQL Data Warehouse-documentatie](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)voor meer informatie over deze service en hoe u deze kunt gebruiken.

## <a name="sql-server-stretch-database"></a>SQL Server Stretch Database
Zie [Azure SQL Server stretch database-documentatie](../sql-server-stretch-database/index.md) voor meer informatie over deze service en hoe u deze kunt gebruiken.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
Zie [Azure Cosmos DB-documentatie](../cosmos-db/index.yml)voor meer informatie over deze service en hoe u deze kunt gebruiken.

### <a name="variations"></a>Verschillen
Azure Cosmos DB is over het algemeen beschikbaar in Azure Government met pariteit voor de open bare versie. Een uitzonde ring is de functie **Azure Cognitive Search toevoegen** op dit moment, die niet beschikbaar is in Cosmos DB voor Azure Government.

De Url's voor het openen van Cosmos DB in Azure Government verschillen ook:

| Servicetype | Open bare Azure | Azure Government |
| --- | --- | --- |
| Cosmos DB | *. documents.azure.com | *. documents.azure.us |


### <a name="considerations"></a>Overwegingen
De volgende informatie bevat de Azure Government grens voor Azure Cosmos DB:

| Gereguleerde/beheerde gegevens toegestaan | Gereguleerde/beheerde gegevens niet toegestaan |
| --- | --- |
| Alle opgeslagen en verwerkte gegevens in Azure Cosmos DB kunnen Azure Government-gereglementeerde gegevens bevatten. |Azure Cosmos DB meta gegevens mogen geen gegevens bevatten die door een export worden beheerd. Voer geen gereguleerde/beheerde gegevens in de volgende velden in: **DB-naam, abonnements naam, resource groepen, resource Tags**. |


## <a name="azure-cache-for-redis"></a>Azure Cache voor Redis
Zie [Azure-cache voor redis-documentatie](../azure-cache-for-redis/index.yml)voor meer informatie over deze service en hoe u deze kunt gebruiken.

### <a name="variations"></a>Verschillen
De Url's voor toegang tot en beheer van Azure cache voor redis in Azure Government verschillen:

| Servicetype | Open bare Azure | Azure Government |
| --- | --- | --- |
| Cache-eind punt |*.redis.cache.windows.net |*.redis.cache.usgovcloudapi.net |

> [!NOTE]
> Alle scripts en code moeten worden verwerkt voor de juiste eind punten en omgevingen. Zie [verbinding maken met andere Clouds](../azure-cache-for-redis/cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds)voor meer informatie.
>
>

### <a name="considerations"></a>Overwegingen
De volgende informatie bevat de Azure Government grens voor Azure cache voor redis:

| Gereguleerde/beheerde gegevens toegestaan | Gereguleerde/beheerde gegevens niet toegestaan |
| --- | --- |
| Alle opgeslagen en verwerkte gegevens in azure cache voor redis kunnen Azure Government gereglementeerde gegevens bevatten. |Azure cache voor redis-meta gegevens mag geen Exporteer bare gegevens bevatten. Voer geen gereguleerde/beheerde gegevens in de volgende velden in: **cache naam, naam van abonnement, resource groepen, resource Tags, redis eigenschappen**. |

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
Zie [Azure database for PostgreSQL-documentatie](../postgresql/index.yml)voor meer informatie over deze service en hoe u deze kunt gebruiken.

### <a name="variations"></a>Verschillen
Geavanceerde beveiliging tegen bedreigingen, Query's met betrekking tot prestaties en aanbevelingen voor Azure Database for PostgreSQL zijn **niet** beschikbaar in azure Government.

De Url's voor het openen en beheren van Azure Database for PostgreSQL in Azure Government verschillen:

| Servicetype | Open bare Azure | Azure Government |
| --- | --- | --- |
| PostgreSQL-eind punt |*. postgres.database.azure.com |*. postgres.database.usgovcloudapi.net |

### <a name="considerations"></a>Overwegingen
De volgende informatie bevat de Azure Government grens voor Azure Database for PostgreSQL:

| Gereguleerde/beheerde gegevens toegestaan | Gereguleerde/beheerde gegevens niet toegestaan |
| --- | --- |
| Alle opgeslagen en verwerkte gegevens in Azure Database for PostgreSQL kunnen Azure Government-gereglementeerde gegevens bevatten. Gebruik database hulpprogramma's voor gegevens overdracht van door Azure Government gereglementeerde gegevens. |Azure Database for PostgreSQL meta gegevens mogen geen gegevens bevatten die door een export worden beheerd. Deze meta gegevens bevatten alle configuratie gegevens die zijn ingevoerd bij het maken en onderhouden van uw opslag product.  Voer geen gereguleerde/beheerde gegevens in de volgende velden in: database naam, naam van abonnement, resource groepen, Server naam, aanmelden bij Server beheerder, implementatie namen, resource namen, resource Tags. |

## <a name="azure-database-for-mariadb"></a>Azure Database for MariaDB
Zie [Azure database for MariaDB-documentatie](../mariadb/index.yml)voor meer informatie over deze service en hoe u deze kunt gebruiken.

### <a name="variations"></a>Verschillen
Query's met betrekking tot prestaties en aanbevelingen voor Azure Database for MariaDB zijn **niet** beschikbaar in azure Government.

De Url's voor het openen en beheren van Azure Database for MariaDB in Azure Government verschillen:

| Servicetype | Open bare Azure | Azure Government |
| --- | --- | --- |
| MariaDB-eind punt |*. mariadb.database.azure.com |*. mariadb.database.usgovcloudapi.net |

### <a name="considerations"></a>Overwegingen
De volgende informatie bevat de Azure Government grens voor Azure Database for MariaDB:

| Gereguleerde/beheerde gegevens toegestaan | Gereguleerde/beheerde gegevens niet toegestaan |
| --- | --- |
| Alle opgeslagen en verwerkte gegevens in Azure Database for MariaDB kunnen Azure Government-gereglementeerde gegevens bevatten. Gebruik database hulpprogramma's voor gegevens overdracht van door Azure Government gereglementeerde gegevens. |Azure Database for MariaDB meta gegevens mogen geen gegevens bevatten die door een export worden beheerd. Deze meta gegevens bevatten alle configuratie gegevens die zijn ingevoerd bij het maken en onderhouden van uw opslag product.  Voer geen gereguleerde/beheerde gegevens in de volgende velden in: database naam, naam van abonnement, resource groepen, Server naam, aanmelden bij Server beheerder, implementatie namen, resource namen, resource Tags. |

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL
Zie [Azure database for MySQL-documentatie](../mysql/index.yml)voor meer informatie over deze service en hoe u deze kunt gebruiken.

### <a name="variations"></a>Verschillen
Geavanceerde beveiliging tegen bedreigingen, Query's met betrekking tot prestaties en aanbevelingen voor Azure Database for MySQL zijn **niet** beschikbaar in azure Government.

De Url's voor het openen en beheren van Azure Database for MySQL in Azure Government verschillen:

| Servicetype | Open bare Azure | Azure Government |
| --- | --- | --- |
| MySQL-eind punt |*. mysql.database.azure.com |*. mysql.database.usgovcloudapi.net |

### <a name="considerations"></a>Overwegingen
De volgende informatie bevat de Azure Government grens voor Azure Database for MySQL:

| Gereguleerde/beheerde gegevens toegestaan | Gereguleerde/beheerde gegevens niet toegestaan |
| --- | --- |
| Alle opgeslagen en verwerkte gegevens in Azure Database for MySQL kunnen Azure Government-gereglementeerde gegevens bevatten. Gebruik database hulpprogramma's voor gegevens overdracht van door Azure Government gereglementeerde gegevens. |Azure Database for MySQL meta gegevens mogen geen gegevens bevatten die door een export worden beheerd. Deze meta gegevens bevatten alle configuratie gegevens die zijn ingevoerd bij het maken en onderhouden van uw opslag product.  Voer geen gereguleerde/beheerde gegevens in de volgende velden in: database naam, naam van abonnement, resource groepen, Server naam, aanmelden bij Server beheerder, implementatie namen, resource namen, resource Tags. |

## <a name="next-steps"></a>Volgende stappen
Voor aanvullende informatie en updates Abonneren op de <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government blog.</a>
