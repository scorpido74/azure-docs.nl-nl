---
title: Query's in clouddatabases met ander schema
description: cross-databasequery's instellen via verticale partities
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: d5983d25685242a696300f293231bbf987e8442d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823736"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Query's in clouddatabases met verschillende schema's (voorbeeld)

![Query's voor tabellen in verschillende databases][1]

Verticaal verdeelde databases maken gebruik van verschillende sets tabellen in verschillende databases. Dat betekent dat het schema anders is in verschillende databases. Alle tabellen voor voorraad bevinden zich bijvoorbeeld in één database, terwijl alle boekhoudgerelateerde tabellen zich in een tweede database bevinden. 

## <a name="prerequisites"></a>Vereisten

* De gebruiker moet beschikken over ALTER ELKE EXTERNE GEGEVENS BRON toestemming. Deze toestemming is opgenomen in de toestemming van ALTER DATABASE.
* WIJZIG ALLE externe gegevensbronmachtigingen zijn nodig om naar de onderliggende gegevensbron te verwijzen.

## <a name="overview"></a>Overzicht

> [!NOTE]
> In tegenstelling tot horizontale partitionering zijn deze DDL-instructies niet afhankelijk van het definiëren van een gegevenslaag met een shardkaart via de elastische databaseclientbibliotheek.
>

1. [HOOFDSLEUTEL MAKEN](https://msdn.microsoft.com/library/ms174382.aspx)
2. [DATABASESCOPED-REFERENTIE MAKEN](https://msdn.microsoft.com/library/mt270260.aspx)
3. [EXTERNE GEGEVENSBRON MAKEN](https://msdn.microsoft.com/library/dn935022.aspx)
4. [EXTERNE TABEL MAKEN](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Hoofdsleutel en referenties met databasescoped maken

De referentie wordt gebruikt door de elastische query om verbinding te maken met uw externe databases.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Zorg ervoor `<username>` dat het achtervoegsel **"servername"\@** niet wordt opgenomen. 
>

## <a name="create-external-data-sources"></a>Externe gegevensbronnen maken

Syntaxis:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

> [!IMPORTANT]
> De parameter TYPE moet worden ingesteld op **RDBMS**. 
>

### <a name="example"></a>Voorbeeld

In het volgende voorbeeld wordt het gebruik van de CREATE-instructie voor externe gegevensbronnen geïllustreerd. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

Ga als u de lijst met huidige externe gegevensbronnen op: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Externe tabellen

Syntaxis:

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 

    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>Voorbeeld

```sql
    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 
```

In het volgende voorbeeld ziet u hoe u de lijst met externe tabellen uit de huidige database ophaalt: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Opmerkingen

Elastische query breidt de bestaande syntaxis van externe tabellen uit om externe tabellen te definiëren die externe gegevensbronnen van het type RDBMS gebruiken. Een externe tabeldefinitie voor verticale partitionering omvat de volgende aspecten: 

* **Schema**: De externe tabel DDL definieert een schema dat uw query's kunnen gebruiken. Het schema in de definitie van uw externe tabel moet overeenkomen met het schema van de tabellen in de externe database waar de werkelijke gegevens worden opgeslagen. 
* **Verwijzing naar externe database:** De externe tabel DDL verwijst naar een externe gegevensbron. De externe gegevensbron geeft de naam van de SQL Database-server en de databasenaam op van de externe database waar de werkelijke tabelgegevens worden opgeslagen. 

Met behulp van een externe gegevensbron zoals beschreven in de vorige sectie, is de syntaxis om externe tabellen te maken als volgt: 

De DATA_SOURCE-clausule definieert de externe gegevensbron (d.w.z. de externe database in het geval van verticale partitionering) die wordt gebruikt voor de externe tabel.  

De SCHEMA_NAME- en OBJECT_NAME clausules bieden de mogelijkheid om de externe tabeldefinitie in kaart te brengen in een tabel in een ander schema in de externe database of aan een tabel met een andere naam. Dit is handig als u een externe tabel wilt definiëren in een catalogusweergave of dmv in uw externe database - of een andere situatie waarin de naam van de externe tabel al lokaal is genomen.  

Met de volgende DDL-instructie wordt een bestaande externe tabeldefinitie uit de lokale catalogus verwijderd. Het heeft geen invloed op de externe database. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Machtigingen voor CREATE/DROP EXTERNE TABEL**: WIJZIG ALLE EXTERNE GEGEVENSBRONmachtigingen zijn nodig voor externe tabel DDL die ook nodig is om naar de onderliggende gegevensbron te verwijzen.  

## <a name="security-considerations"></a>Beveiligingsoverwegingen

Gebruikers met toegang tot de externe tabel krijgen automatisch toegang tot de onderliggende externe tabellen onder de referentie die is opgegeven in de definitie van externe gegevensbron. U moet de toegang tot de externe tabel zorgvuldig beheren om ongewenste verhoging van bevoegdheden via de referentie van de externe gegevensbron te voorkomen. Reguliere SQL-machtigingen kunnen worden gebruikt om toegang tot een externe tabel te verlenen of in te trekken, net alsof het een gewone tabel is.  

## <a name="example-querying-vertically-partitioned-databases"></a>Voorbeeld: verticaal gepartitioneerde databases opvragen

In de volgende query wordt een drierichtingsverbinding uitgevoerd tussen de twee lokale tabellen voor bestellingen en orderregels en de externe tabel voor klanten. Dit is een voorbeeld van de gebruiksaanvraag voor referentiegegevens voor elastische query's: 

```sql
    SELECT      
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline, 
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer 
    JOIN orders 
    ON c_id = o_c_id
    JOIN  order_line 
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Opgeslagen procedure voor externe T-SQL-uitvoering: sp\_execute_remote

Elastische query introduceert ook een opgeslagen procedure die directe toegang tot de externe database biedt. De opgeslagen procedure heet [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714) en kan worden gebruikt om externe opgeslagen procedures of T-SQL-code uit te voeren op de externe database. Er zijn de volgende parameters nodig: 

* Naam van de gegevensbron (nvarchar): De naam van de externe gegevensbron van type RDBMS. 
* Query (nvarchar): de T-SQL-query die moet worden uitgevoerd op de externe database. 
* Parameterdeclaratie (nvarchar) - optioneel: Tekenreeks met definities van gegevenstype voor de parameters die worden gebruikt in de parameter Query (zoals sp_executesql). 
* Parameterwaardelijst - optioneel: door komma's gescheiden lijst met parameterwaarden (zoals sp_executesql).

De\_sp\_execute remote gebruikt de externe gegevensbron in de aanroepparameters om de gegeven T-SQL-instructie op de externe database uit te voeren. Het maakt gebruik van de referentie van de externe gegevensbron om verbinding te maken met de externe database.  

Voorbeeld: 

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 
```

## <a name="connectivity-for-tools"></a>Connectiviteit voor gereedschappen

U reguliere SQL Server-verbindingstekenreeksen gebruiken om uw BI- en gegevensintegratietools te verbinden met databases op de SQL DB-server waarop elastische query's zijn ingeschakeld en externe tabellen zijn gedefinieerd. Zorg ervoor dat SQL Server wordt ondersteund als gegevensbron voor uw hulpprogramma. Raadpleeg vervolgens de elastische querydatabase en de externe tabellen, net als elke andere SQL Server-database waarmee u verbinding zou maken met uw tool. 

## <a name="best-practices"></a>Aanbevolen procedures

* Controleer of de elastische queryeindpuntdatabase toegang heeft gekregen tot de externe database door toegang voor Azure Services in te schakelen in de SQL DB-firewallconfiguratie. Zorg er ook voor dat de referentie in de externe gegevensbrondefinitie met succes kan worden aangemeld bij de externe database en dat de machtigingen zijn om toegang te krijgen tot de externe tabel.  
* Elastische query werkt het beste voor query's waar het grootste deel van de berekening kan worden gedaan op de externe databases. U krijgt meestal de beste queryprestaties met selectieve filterpredicaten die kunnen worden geëvalueerd op de externe databases of joins die volledig kunnen worden uitgevoerd in de externe database. Andere querypatronen moeten mogelijk grote hoeveelheden gegevens uit de externe database laden en kunnen slecht presteren. 

## <a name="next-steps"></a>Volgende stappen

* Zie Overzicht van elastische query's voor een overzicht van elastische [query's](sql-database-elastic-query-overview.md).
* Zie Aan de slag [met cross-databasequery (verticale partitionering)](sql-database-elastic-query-getting-started-vertical.md)voor een verticale zelfstudie voor het partitioneren.
* Zie [Aan de slag met elastische query voor horizontale partitionering (sharding) voor](sql-database-elastic-query-getting-started.md)een horizontale zelfstudie voor het partitioneren (sharden).
* Zie [Horizontaal gepartitioneerde gegevens opvragen voor](sql-database-elastic-query-horizontal-partitioning.md) syntaxis- en voorbeeldquery's voor horizontaal verdeelde gegevens)
* Zie [\_sp \_remote uitvoeren](https://msdn.microsoft.com/library/mt703714) voor een opgeslagen procedure die een Transact-SQL-instructie uitvoert op één externe Azure SQL-database of set databases die als shards dienen in een horizontaal partitioneringsschema.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
