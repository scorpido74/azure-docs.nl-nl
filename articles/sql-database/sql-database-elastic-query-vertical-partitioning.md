---
title: Query's uitvoeren voor clouddatabases met verschillende schema’s
description: query's voor meerdere data bases instellen voor verticale partities
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823736"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Query's uitvoeren in Cloud databases met verschillende schema's (preview-versie)

![Query's uitvoeren voor meerdere tabellen in verschillende data bases][1]

Verticaal gepartitioneerde data bases gebruiken verschillende sets tabellen in verschillende data bases. Dit betekent dat het schema afwijkt van verschillende data bases. Zo bevinden alle tabellen voor de inventarisatie zich op één data base, terwijl alle aan de administratie gerelateerde tabellen zich in een tweede data base bevinden. 

## <a name="prerequisites"></a>Vereisten

* De gebruiker moet beschikken over de machtiging voor het wijzigen van externe gegevens bronnen. Deze machtiging is opgenomen in de machtiging ALTER data base.
* Machtigingen voor externe gegevens bronnen wijzigen is nodig om te verwijzen naar de onderliggende gegevens bron.

## <a name="overview"></a>Overzicht

> [!NOTE]
> In tegens telling tot horizontale partitionering zijn deze DDL-instructies niet afhankelijk van het definiëren van een gegevenslaag met een Shard-toewijzing via de client bibliotheek voor Elastic data base.
>

1. [HOOFD SLEUTEL MAKEN](https://msdn.microsoft.com/library/ms174382.aspx)
2. [DATA BASE-SCOPED REFERENTIE MAKEN](https://msdn.microsoft.com/library/mt270260.aspx)
3. [EXTERNE GEGEVENS BRON MAKEN](https://msdn.microsoft.com/library/dn935022.aspx)
4. [EXTERNE TABEL MAKEN](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Data base-scoped Master sleutel en referenties maken

De referentie wordt gebruikt door de elastische query om verbinding te maken met uw externe data bases.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Zorg ervoor dat de `<username>` geen achtervoegsel voor **'\@servername '** bevat. 
>

## <a name="create-external-data-sources"></a>Externe gegevens bronnen maken

Syntaxis

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

> [!IMPORTANT]
> De TYPE parameter moet worden ingesteld op **RDBMS**. 
>

### <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u het gebruik van de instructie CREATE voor externe gegevens bronnen. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

De lijst met huidige externe gegevens bronnen ophalen: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Externe tabellen

Syntaxis

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

In het volgende voor beeld ziet u hoe u de lijst met externe tabellen kunt ophalen uit de huidige Data Base: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Opmerkingen

Met elastische query's wordt de bestaande syntaxis van de externe tabel uitgebreid met het definiëren van externe tabellen die gebruikmaken van externe gegevens bronnen van het type RDBMS. Een externe tabel definitie voor verticale partitionering heeft betrekking op de volgende aspecten: 

* **Schema**: de externe tabel DDL definieert een schema dat door uw query's kan worden gebruikt. Het schema dat is opgegeven in de definitie van de externe tabel moet overeenkomen met het schema van de tabellen in de externe data base waarin de daad werkelijke gegevens zijn opgeslagen. 
* **Externe database referentie**: de externe tabel DDL verwijst naar een externe gegevens bron. De externe gegevens bron specificeert de SQL Database Server naam en de database naam van de externe data base waar de gegevens van de actuele tabel worden opgeslagen. 

Als u een externe gegevens bron gebruikt, zoals beschreven in de vorige sectie, is de syntaxis voor het maken van externe tabellen als volgt: 

De DATA_SOURCE-component definieert de externe gegevens bron (dat wil zeggen de externe data base in het geval van verticale partitionering) die wordt gebruikt voor de externe tabel.  

De componenten SCHEMA_NAME en OBJECT_NAME bieden de mogelijkheid om de definitie van de externe tabel toe te wijzen aan een tabel in een ander schema op de externe data base of aan een tabel met een andere naam. Dit is handig als u een externe tabel wilt definiëren in een catalogus weergave of DMV op uw externe data base, of een andere situatie waarin de naam van de externe tabel al lokaal wordt overgenomen.  

De volgende DDL-instructie verwijdert een bestaande definitie van een externe tabel uit de lokale catalogus. Dit heeft geen invloed op de externe data base. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Machtigingen voor maken/verwijderen van externe tabel**: machtigingen voor externe gegevens bronnen wijzigen is vereist voor de DDL van externe tabellen die ook nodig is om te verwijzen naar de onderliggende gegevens bron.  

## <a name="security-considerations"></a>Beveiligingsoverwegingen

Gebruikers met toegang tot de externe tabel krijgen automatisch toegang tot de onderliggende externe tabellen onder de referentie die is opgegeven in de definitie van de externe gegevens bron. U moet de toegang tot de externe tabel zorgvuldig beheren om te voor komen dat er ongewenste bevoegdheden worden uitgebreid via de referentie van de externe gegevens bron. Reguliere SQL-machtigingen kunnen worden gebruikt om toegang tot een externe tabel toe te kennen of in te trekken, net alsof het een normale tabel is.  

## <a name="example-querying-vertically-partitioned-databases"></a>Voor beeld: een query uitvoeren op verticaal gepartitioneerde data bases

Met de volgende query wordt een drie richtings relatie uitgevoerd tussen de twee lokale tabellen voor orders en order regels en de externe tabel voor klanten. Dit is een voor beeld van het gebruik van referentie gegevens voor elastische query's: 

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

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Opgeslagen procedure voor externe T-SQL-uitvoering: SP\_execute_remote

Met elastische query's wordt ook een opgeslagen procedure geïntroduceerd die directe toegang biedt tot de externe data base. De opgeslagen procedure heet [sp\_execute \_Remote](https://msdn.microsoft.com/library/mt703714) en kan worden gebruikt om externe opgeslagen procedures of t-SQL-code uit te voeren op de externe data base. Hierbij worden de volgende para meters gebruikt: 

* Naam van de gegevens bron (nvarchar): de naam van de externe gegevens bron van het type RDBMS. 
* Query (nvarchar): de T-SQL-query die moet worden uitgevoerd op de externe data base. 
* Parameter declaratie (nvarchar)-optioneel: string met definities van gegevens typen voor de para meters die worden gebruikt in de query parameter (zoals sp_executesql). 
* Lijst met parameter waarden-optioneel: door Komma's gescheiden lijst met parameter waarden (zoals sp_executesql).

De SP\_Execute\_Remote gebruikt de externe gegevens bron die in de aanroep parameters is opgegeven om de opgegeven T-SQL-instructie uit te voeren op de externe data base. De referentie van de externe gegevens bron wordt gebruikt om verbinding te maken met de externe data base.  

Voorbeeld: 

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 
```

## <a name="connectivity-for-tools"></a>Connectiviteit voor hulpprogram ma's

U kunt gewone SQL Server verbindings reeksen gebruiken om uw BI-en gegevens integratie hulpprogramma's te koppelen aan data bases op de SQL DB-server waarop elastische query's zijn ingeschakeld en externe tabellen zijn gedefinieerd. Zorg ervoor dat SQL Server wordt ondersteund als gegevens bron voor uw hulp programma. Raadpleeg vervolgens de elastische query database en de externe tabellen, net als andere SQL Server Data Base waarmee u verbinding wilt maken met uw hulp programma. 

## <a name="best-practices"></a>Aanbevolen procedures

* Zorg ervoor dat de data base van het Elastic query-eind punt toegang heeft gekregen tot de externe data base door toegang in te scha kelen voor Azure-Services in de configuratie van de SQL DB-firewall. Zorg er ook voor dat de referenties die in de definitie van de externe gegevens bron zijn opgenomen, kunnen worden aangemeld bij de externe data base en de machtigingen hebben voor toegang tot de externe tabel.  
* Elastische query's werken het beste voor query's waarbij de meeste berekeningen kunnen worden uitgevoerd voor de externe data bases. Normaal gesp roken krijgt u de beste query prestaties met selectieve filter predikaten die kunnen worden geëvalueerd op de externe data bases of samen voegingen die volledig kunnen worden uitgevoerd op de externe data base. Andere query patronen moeten mogelijk grote hoeveel heden gegevens van de externe data base laden en kunnen goed worden uitgevoerd. 

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van elastische query's](sql-database-elastic-query-overview.md)voor een overzicht van elastische query's.
* Zie aan de slag [met query's tussen data bases (verticaal partitioneren)](sql-database-elastic-query-getting-started-vertical.md)voor een verticaal gepartitioneerde zelf studie.
* Zie aan de slag [met elastische query's voor horizontale partitionering (sharding)](sql-database-elastic-query-getting-started.md)voor een zelf studie over horizontale partitionering (sharding).
* Zie query's [uitvoeren in horizon taal gepartitioneerde gegevens](sql-database-elastic-query-horizontal-partitioning.md) voor syntaxis-en voorbeeld query's voor Horizon taal gepartitioneerde gegevens)
* Zie [sp\_execute \_Remote](https://msdn.microsoft.com/library/mt703714) voor een opgeslagen procedure waarmee een Transact-SQL-instructie wordt uitgevoerd op één externe Azure SQL database of een set met data bases die fungeren als Shards in een horizon taal partitie schema.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
