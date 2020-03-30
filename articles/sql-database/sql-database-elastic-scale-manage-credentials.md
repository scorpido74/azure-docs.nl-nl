---
title: Referenties beheren in de elastische databaseclientbibliotheek
description: Het juiste niveau van referenties instellen, beheerder op alleen-lezen, voor elastische database-apps
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 91689a32a128584aade8081905e3d1aa3ecb0a97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823580"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Referenties die worden gebruikt om toegang te krijgen tot de elastic database-clientbibliotheek

De [elastic database-clientbibliotheek](sql-database-elastic-database-client-library.md) gebruikt drie verschillende soorten referenties om toegang te krijgen tot de [shardmapbeheerder.](sql-database-elastic-scale-shard-map-management.md) Afhankelijk van de noodzaak, gebruik maken van de referentie met het laagste niveau van toegang mogelijk.

* **Managementreferenties:** voor het maken of manipuleren van een shardmapmanager. (Zie de [woordenlijst](sql-database-elastic-scale-glossary.md).)
* **Toegangsreferenties:** toegang krijgen tot een bestaande shardmapmanager om informatie over shards te verkrijgen.
* **Verbindingsreferenties:** verbinding maken met shards.

Zie ook [Databases en aanmeldingen beheren in Azure SQL Database.](sql-database-manage-logins.md)

## <a name="about-management-credentials"></a>Informatie over managementreferenties

Managementreferenties worden gebruikt om een **ShardMapManager** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)object te maken voor toepassingen die shardmaps manipuleren. (Zie bijvoorbeeld [Een shard toevoegen met behulp van hulpmiddelen voor elasticdatabase](sql-database-elastic-scale-add-a-shard.md) en [gegevensafhankelijke routering).](sql-database-elastic-scale-data-dependent-routing.md) De gebruiker van de elastic scale client library maakt de SQL-gebruikers en SQL-aanmeldingen en zorgt ervoor dat elk de lees-/schrijfmachtigingen krijgt voor de wereldwijde shardkaartdatabase en alle sharddatabases. Deze referenties worden gebruikt om de globale shardkaart en de lokale shardkaarten te onderhouden wanneer wijzigingen in de shardkaart worden uitgevoerd. Gebruik bijvoorbeeld de beheerreferenties om het object shard mapmanager te maken (met **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET):](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

De variabele **smmAdminConnectionString** is een verbindingstekenreeks die de beheerreferenties bevat. De gebruikersnaam en het wachtwoord bieden lees-/schrijftoegang tot zowel de shardkaartdatabase als de afzonderlijke shards. De tekenreeks voor beheerverbindingen bevat ook de naam van de server en de naam van de database om de database met globale shardkaarten te identificeren. Hier is een typische verbindingstekenreeks voor dat doel:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Gebruik geen waarden in deusername@servervorm van " "-in plaats daarvan gewoon gebruik maken van de "gebruikersnaam" waarde.  Dit komt omdat referenties moeten werken tegen zowel de shard map manager database en individuele shards, die kunnen worden op verschillende servers.

## <a name="access-credentials"></a>Toegangsreferenties

Wanneer u een shardmapbeheerder maakt in een toepassing die geen shardkaarten beheert, gebruikt u referenties met alleen-lezen machtigingen op de globale shardkaart. De informatie die wordt opgehaald uit de globale shardkaart onder deze referenties wordt gebruikt voor [gegevensafhankelijke routering](sql-database-elastic-scale-data-dependent-routing.md) en om de shardmapcache op de client te vullen. De referenties worden via hetzelfde gesprekspatroon aan **GetSqlShardMapManager**geleverd:

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Let op het gebruik van de **smmReadOnlyConnectionString** om het gebruik van verschillende referenties voor deze toegang namens **niet-beheerdersgebruikers** weer te geven: deze referenties mogen geen schrijfmachtigingen bieden op de globale shardkaart.

## <a name="connection-credentials"></a>Verbindingsreferenties

Aanvullende referenties zijn nodig bij het gebruik van de **methode OpenConnectionForKey** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)om toegang te krijgen tot een shard die is gekoppeld aan een shardsleutel. Deze referenties moeten machtigingen bieden voor alleen-lezen toegang tot de lokale shardkaarttabellen die op de shard staan. Dit is nodig om verbindingsvalidatie uit te voeren voor gegevensafhankelijke routering op de shard. Dit codefragment biedt toegang tot gegevens in de context van gegevensafhankelijke routering:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

In dit voorbeeld bevat **smmUserConnectionString** de verbindingstekenreeks voor de gebruikersreferenties. Voor Azure SQL DB is dit een typische verbindingstekenreeks voor gebruikersreferenties:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Net als bij de beheerdersreferenties, gebruik geenusername@serverwaarden in de vorm van " ". In plaats daarvan, gewoon gebruik maken van "gebruikersnaam".  Houd er ook rekening mee dat de verbindingstekenreeks geen servernaam en databasenaam bevat. Dat komt omdat het **OpenConnectionForKey-gesprek** automatisch de verbinding met de juiste shard stuurt op basis van de sleutel. Daarom worden de naam van de database en de naam van de server niet opgegeven.

## <a name="see-also"></a>Zie ook

[Databases en aanmeldingen beheren in Azure SQL Database](sql-database-manage-logins.md)

[Uw SQL Database beveiligen](sql-database-security-overview.md)

[Elastische databasetaken](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
