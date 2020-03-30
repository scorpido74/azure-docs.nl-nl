---
title: Rapportage in geschaalde clouddatabases
description: elastische query's instellen boven horizontale partities
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/03/2019
ms.openlocfilehash: 79abaade22fc107fa4c848607ff48232eeeb58ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823765"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Rapportage over geschaalde clouddatabases (preview)

![Query over shards][1]

Gesharddatabases distribueren rijen over een geschaalde gegevenslaag. Het schema is identiek op alle deelnemende databases, ook wel horizontale partitionering genoemd. Met behulp van een elastische query u rapporten maken die alle databases in een geshard database omvatten.

Zie Rapportage in [geschaalde clouddatabases](sql-database-elastic-query-getting-started.md)voor een snelle start.

Zie [Query in clouddatabases met verschillende schema's](sql-database-elastic-query-vertical-partitioning.md)voor niet-geshard databases.

## <a name="prerequisites"></a>Vereisten

* Maak een shardkaart met behulp van de elastische databaseclientbibliotheek. zie [Shard map management](sql-database-elastic-scale-shard-map-management.md). Of gebruik de voorbeeld-app in [Aan de slag met elastische databasetools.](sql-database-elastic-scale-get-started.md)
* U ook [bestaande databases migreren naar geschaalde databases](sql-database-elastic-convert-to-use-elastic-tools.md).
* De gebruiker moet beschikken over ALTER ELKE EXTERNE GEGEVENS BRON toestemming. Deze toestemming is opgenomen in de toestemming van ALTER DATABASE.
* WIJZIG ALLE externe gegevensbronmachtigingen zijn nodig om naar de onderliggende gegevensbron te verwijzen.

## <a name="overview"></a>Overzicht

Met deze instructies wordt de weergave van metagegevens gemaakt van uw geshard gegevenslaag in de elastische querydatabase.

1. [HOOFDSLEUTEL MAKEN](https://msdn.microsoft.com/library/ms174382.aspx)
2. [DATABASESCOPED-REFERENTIE MAKEN](https://msdn.microsoft.com/library/mt270260.aspx)
3. [EXTERNE GEGEVENSBRON MAKEN](https://msdn.microsoft.com/library/dn935022.aspx)
4. [EXTERNE TABEL MAKEN](https://msdn.microsoft.com/library/dn935021.aspx)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 Hoofdsleutel en referenties met databasescopen maken

De referentie wordt gebruikt door de elastische query om verbinding te maken met uw externe databases.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Zorg ervoor dat de *"\<gebruikersnaam\>"* geen *"servername"\@* achtervoegsel bevat.

## <a name="12-create-external-data-sources"></a>1.2 Externe gegevensbronnen maken

Syntaxis:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
            (TYPE = SHARD_MAP_MANAGER,
                       LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>,
            SHARD_MAP_NAME = ‘<shardmapname>’
                   ) [;]

### <a name="example"></a>Voorbeeld

    CREATE EXTERNAL DATA SOURCE MyExtSrc
    WITH
    (
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net',
        DATABASE_NAME='ShardMapDatabase',
        CREDENTIAL= SMMUser,
        SHARD_MAP_NAME='ShardMap'
    );

De lijst met huidige externe gegevensbronnen ophalen:

    select * from sys.external_data_sources;

De externe gegevensbron verwijst naar uw shardkaart. Een elastische query gebruikt vervolgens de externe gegevensbron en de onderliggende shardkaart om de databases op te sommen die deelnemen aan de gegevenslaag.
Dezelfde referenties worden gebruikt om de shardkaart te lezen en toegang te krijgen tot de gegevens op de scherven tijdens de verwerking van een elastische query.

## <a name="13-create-external-tables"></a>1.3 Externe tabellen maken

Syntaxis:  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  

    <sharded_external_table_options> ::=
      DATA_SOURCE = <External_Data_Source>,
      [ SCHEMA_NAME = N'nonescaped_schema_name',]
      [ OBJECT_NAME = N'nonescaped_object_name',]
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Voorbeeld**

    CREATE EXTERNAL TABLE [dbo].[order_line](
         [ol_o_id] int NOT NULL,
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL,
         [ol_number] tinyint NOT NULL,
         [ol_i_id] int NOT NULL,
         [ol_delivery_d] datetime NOT NULL,
         [ol_amount] smallmoney NOT NULL,
         [ol_supply_w_id] int NOT NULL,
         [ol_quantity] smallint NOT NULL,
         [ol_dist_info] char(24) NOT NULL
    )

    WITH
    (
        DATA_SOURCE = MyExtSrc,
         SCHEMA_NAME = 'orders',
         OBJECT_NAME = 'order_details',
        DISTRIBUTION=SHARDED(ol_w_id)
    );

Haal de lijst met externe tabellen op uit de huidige database:

    SELECT * from sys.external_tables;

Externe tabellen neerzetten:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Opmerkingen

De\_gegevensbronclausule definieert de externe gegevensbron (een shardkaart) die wordt gebruikt voor de externe tabel.  

De\_clausules\_SCHEMANAAM en OBJECTNAAM brengen de externe tabeldefinitie in kaart aan een tabel in een ander schema. Als het schema van het externe object wordt weggelaten, wordt ervan uitgegaan dat het "dbo" is en wordt aangenomen dat de naam ervan identiek is aan de externe tabelnaam die wordt gedefinieerd. Dit is handig als de naam van uw externe tabel al is opgenomen in de database waar u de externe tabel wilt maken. U wilt bijvoorbeeld een externe tabel definiëren om een geaggregeerde weergave van catalogusweergaven of DMVs op uw uitgeschaalde gegevenslaag te krijgen. Aangezien catalogusweergaven en DMVs al lokaal bestaan, u hun namen niet gebruiken voor de externe tabeldefinitie. Gebruik in plaats daarvan een andere naam en gebruik de catalogusweergave\_of de naam\_van de DMV in de besterij s-codenaam en/of objectnaam. (Zie het voorbeeld hieronder.)

De DISTRIBUTIE-clausule geeft de gegevensverdeling aan die voor deze tabel wordt gebruikt. De queryprocessor maakt gebruik van de informatie in de DISTRIBUTIE-clausule om de meest efficiënte queryplannen te maken.

1. **SHARDED** betekent dat gegevens horizontaal worden verdeeld over de databases. De partitiesleutel voor de gegevensdistributie is de **<sharding_column_name>** parameter.
2. **GEREPLICEERD** betekent dat identieke kopieën van de tabel in elke database aanwezig zijn. Het is uw verantwoordelijkheid om ervoor te zorgen dat de replica's identiek zijn in de databases.
3. **ROUND\_ROBIN** betekent dat de tabel horizontaal wordt verdeeld met behulp van een toepassingsafhankelijke distributiemethode.

**Gegevenslaagverwijzing**: De externe tabel DDL verwijst naar een externe gegevensbron. De externe gegevensbron geeft een shardkaart op die de externe tabel de informatie geeft die nodig is om alle databases in uw gegevenslaag te vinden.

### <a name="security-considerations"></a>Beveiligingsoverwegingen

Gebruikers met toegang tot de externe tabel krijgen automatisch toegang tot de onderliggende externe tabellen onder de referentie die is opgegeven in de definitie van externe gegevensbron. Vermijd ongewenste verhoging van bevoegdheden via de referentie van de externe gegevensbron. Gebruik GRANT of INTREKKEN voor een externe tabel alsof het een gewone tabel is.  

Zodra u uw externe gegevensbron en uw externe tabellen hebt gedefinieerd, u nu volledige T-SQL gebruiken via uw externe tabellen.

## <a name="example-querying-horizontal-partitioned-databases"></a>Voorbeeld: horizontale databases met partitie opvragen

In de volgende query wordt een drierichtingssamenwerking tussen magazijnen, orders en orderregels uitgevoerd en worden verschillende aggregaten en een selectief filter gebruikt. Het gaat ervan uit (1) horizontale partitionering (sharding) en (2) dat magazijnen, orders en orderregels worden geshard door de magazijn-id kolom, en dat de elastische query kan co-lokaliseren van de joins op de scherven en het dure deel van de query op de scherven in verwerken Parallelle.

```sql
    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount,
         min(ol_delivery_d) as min_deliv_date
    from warehouse
    join orders
    on w_id = o_w_id
    join order_line
    on o_id = ol_o_id and o_w_id = ol_w_id
    where w_id > 100 and w_id < 200
    group by w_id, o_c_id
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Opgeslagen procedure voor externe T-SQL-uitvoering: sp\_execute_remote

Elastische query introduceert ook een opgeslagen procedure die directe toegang tot de scherven biedt. De opgeslagen procedure heet [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714) en kan worden gebruikt om externe opgeslagen procedures of T-SQL-code uit te voeren op de externe databases. Er zijn de volgende parameters nodig:

* Naam van de gegevensbron (nvarchar): De naam van de externe gegevensbron van type RDBMS.
* Query (nvarchar): de T-SQL-query die op elke shard moet worden uitgevoerd.
* Parameterdeclaratie (nvarchar) - optioneel: Tekenreeks met definities van gegevenstype voor de parameters die worden gebruikt in de parameter Query (zoals sp_executesql).
* Parameterwaardelijst - optioneel: door komma's gescheiden lijst met parameterwaarden (zoals sp_executesql).

De\_sp\_execute remote gebruikt de externe gegevensbron in de aanroepparameters om de gegeven T-SQL-instructie op de externe databases uit te voeren. Het maakt gebruik van de referentie van de externe gegevensbron om verbinding te maken met de shardmap manager database en de externe databases.  

Voorbeeld:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Connectiviteit voor gereedschappen

Gebruik reguliere SQL Server-verbindingstekenreeksen om uw toepassing, uw BI- en gegevensintegratietools met de database te verbinden met uw externe tabeldefinities. Zorg ervoor dat SQL Server wordt ondersteund als gegevensbron voor uw hulpprogramma. Verwijs vervolgens naar de elastische querydatabase zoals elke andere SQL Server-database die met het hulpprogramma is verbonden, en gebruik externe tabellen van uw hulpprogramma of toepassing alsof het lokale tabellen zijn.

## <a name="best-practices"></a>Aanbevolen procedures

* Zorg ervoor dat de elastic query endpoint-database toegang heeft gekregen tot de shardmap-database en alle shards via de SQL DB-firewalls.  
* De gegevensverdeling die door de externe tabel is gedefinieerd, valideren of afdwingen. Als uw werkelijke gegevensdistributie afneemt van de verdeling die is opgegeven in de tabeldefinitie, kunnen uw query's onverwachte resultaten opleveren.
* Elastische query voert momenteel geen shardeliminatie uit wanneer predicaten over de sharding-toets het mogelijk maken om bepaalde scherven veilig uit te sluiten van verwerking.
* Elastische query werkt het beste voor query's waar het grootste deel van de berekening kan worden gedaan op de scherven. U krijgt meestal de beste queryprestaties met selectieve filterpredicaten die kunnen worden geëvalueerd op de shards of worden gevoegt over de partitionerende sleutels die op een op partitie afgestemde manier op alle shards kunnen worden uitgevoerd. Andere querypatronen moeten mogelijk grote hoeveelheden gegevens van de scherven naar het hoofdknooppunt laden en kunnen slecht presteren

## <a name="next-steps"></a>Volgende stappen

* Zie Overzicht van elastische query's voor een overzicht van elastische [query's](sql-database-elastic-query-overview.md).
* Zie Aan de slag [met cross-databasequery (verticale partitionering)](sql-database-elastic-query-getting-started-vertical.md)voor een verticale zelfstudie voor het partitioneren.
* Zie [Verticaal verdeelde gegevens opvragen voor](sql-database-elastic-query-vertical-partitioning.md) syntaxis- en voorbeeldquery's voor verticaal verdeelde gegevens)
* Zie [Aan de slag met elastische query voor horizontale partitionering (sharding) voor](sql-database-elastic-query-getting-started.md)een horizontale zelfstudie voor het partitioneren (sharden).
* Zie [\_sp \_remote uitvoeren](https://msdn.microsoft.com/library/mt703714) voor een opgeslagen procedure die een Transact-SQL-instructie uitvoert op één externe Azure SQL-database of set databases die als shards dienen in een horizontaal partitioneringsschema.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
