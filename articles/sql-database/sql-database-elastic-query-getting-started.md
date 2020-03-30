---
title: Rapporteren in geschaalde clouddatabases
description: Gebruik databasequery's voor meerdere databases om te rapporteren in meerdere databases.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 10/10/2019
ms.openlocfilehash: bad52b364dc83994e7985fc80b1b9f9e7f50481e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823778"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Rapporteren over geschaalde clouddatabases (voorbeeld)

U rapporten maken uit meerdere Azure SQL-databases vanuit één verbindingspunt met behulp van een [elastische query.](sql-database-elastic-query-overview.md) De databases moeten horizontaal worden verdeeld (ook wel bekend als "geshard").

Zie Bestaande databases migreren [naar geschaalde databases](sql-database-elastic-convert-to-use-elastic-tools.md)als u een bestaande database hebt.

Zie [Query in horizontaal gepartitioneerde databases](sql-database-elastic-query-horizontal-partitioning.md)om inzicht te krijgen in de SQL-objecten die nodig zijn om query's op te vragen.

## <a name="prerequisites"></a>Vereisten

Download en voer het [voorbeeld van elastic database-hulpprogramma's](sql-database-elastic-scale-get-started.md)uit.

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Een shardkaartbeheer maken met de voorbeeld-app
Hier maak je een shard map manager samen met verschillende scherven, gevolgd door het invoegen van gegevens in de scherven. Als u toevallig al shards hebt ingesteld met geshardgegevens, u de volgende stappen overslaan en naar de volgende sectie gaan.

1. De voorbeeldtoepassing **Van de hulpmiddelen voor Elastic Database** bouwen en uitvoeren door de stappen in de artikelsectie Downloaden en [uitvoeren.](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app-1) Zodra u alle stappen hebt voltooid, ziet u de volgende opdrachtprompt:

    ![Opdrachtprompt][1]
2. Typ '1' in het opdrachtvenster en druk op **Enter**. Hierdoor wordt de shardmapmanager gemaakt en worden twee scherven aan de server toegevoegd. Typ vervolgens "3" en druk op **Enter**; herhaal de actie vier keer. Hiermee worden voorbeeldgegevensrijen in uw shards ingevoegd.
3. De [Azure-portal](https://portal.azure.com) moet drie nieuwe databases in uw server weergeven:

   ![Bevestiging van Visual Studio][2]

   Op dit moment worden query's met meerdere gegevens ondersteund via de clientbibliotheken van elastic database. Gebruik bijvoorbeeld optie 4 in het opdrachtvenster. De resultaten van een multi-shard query zijn altijd een **unie alle** resultaten van alle scherven.

   In de volgende sectie maken we een voorbeelddatabaseeindpunt dat rijkere query's van de gegevens over shards ondersteunt.

## <a name="create-an-elastic-query-database"></a>Een elastische querydatabase maken
1. Open de [Azure-portal](https://portal.azure.com) en log in.
2. Maak een nieuwe Azure SQL-database op dezelfde server als uw shard-installatie. Geef de naam van de database 'ElasticDBQuery'.

    ![Azure-portal en prijscategorie][3]

    > [!NOTE]
    > u een bestaande database gebruiken. Als u dit doen, mag het niet een van de scherven zijn waarop u uw query's wilt uitvoeren. Deze database wordt gebruikt voor het maken van de metagegevensobjecten voor een elastische databasequery.
    >

## <a name="create-database-objects"></a>Databaseobjecten maken
### <a name="database-scoped-master-key-and-credentials"></a>Hoofdsleutel en referenties met databasebereik
Deze worden gebruikt om verbinding te maken met de shard map manager en de scherven:

1. Open SQL Server Management Studio of SQL Server Data Tools in Visual Studio.
2. Maak verbinding met de ElasticDBQuery-database en voer de volgende T-SQL-opdrachten uit:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "gebruikersnaam" en "wachtwoord" moet hetzelfde zijn als inloggegevens die worden gebruikt in stap 3 van sectie [Download en voer de voorbeeld-app uit](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) in het artikel Aan de slag met elastic **database-hulpprogramma's.**

### <a name="external-data-sources"></a>Externe gegevensbronnen
Als u een externe gegevensbron wilt maken, voert u de volgende opdracht uit in de ElasticDBQuery-database:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" is de naam van de shardkaart, als u de shardkaart en de shardkaartbeheerder hebt gemaakt met behulp van het voorbeeld van elastische databasegereedschappen. Als u echter uw aangepaste installatie voor dit voorbeeld hebt gebruikt, moet dit de shardkaartnaam zijn die u in uw toepassing hebt gekozen.

### <a name="external-tables"></a>Externe tabellen
Maak een externe tabel die overeenkomt met de tabel Klanten op de shards door de volgende opdracht uit te voeren in de ElasticDBQuery-database:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Een voorbeeld van een elastische database T-SQL-query uitvoeren
Zodra u uw externe gegevensbron en uw externe tabellen hebt gedefinieerd, u nu volledige T-SQL gebruiken via uw externe tabellen.

Voer deze query uit in de ElasticDBQuery-database:

    select count(CustomerId) from [dbo].[Customers]

U zult merken dat de query de resultaten van alle shards verzamelt en de volgende uitvoer geeft:

![Uitvoerdetails][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Elastische databasequeryresultaten importeren in Excel
 U de resultaten van een query importeren in een Excel-bestand.

1. Start Excel 2013.
2. Navigeer naar het lint **Gegevens.**
3. Klik **op Uit andere bronnen** en klik op Van SQL **Server**.

   ![Excel importeren uit andere bronnen][5]
4. Typ in de **wizard Gegevensverbinding** de servernaam en inloggegevens. Klik vervolgens op **Volgende**.
5. Selecteer in het dialoogvenster **Selecteer de database met de gewenste gegevens**de **ElasticDBQuery-database.**
6. Selecteer de tabel **Klanten** in de lijstweergave en klik op **Volgende**. Klik vervolgens op **Voltooien**.
7. Selecteer **Tabel** en klik op **OK**in het formulier **Gegevens importeren** onder **Selecteren hoe u deze gegevens in uw werkmap wilt weergeven.**

Alle rijen uit de tabel **Klanten,** die in verschillende shards zijn opgeslagen, vullen het Excel-blad.

U nu de krachtige gegevensvisualisatiefuncties van Excel gebruiken. U de verbindingstekenreeks met uw servernaam, databasenaam en referenties gebruiken om uw BI- en gegevensintegratietools te verbinden met de elastische querydatabase. Zorg ervoor dat SQL Server wordt ondersteund als gegevensbron voor uw hulpprogramma. U verwijzen naar de elastische querydatabase en externe tabellen, net als elke andere SQL Server-database en SQL Server-tabellen waarmee u verbinding zou maken met uw tool.

### <a name="cost"></a>Kosten
Er worden geen extra kosten in rekening gebracht voor het gebruik van de functie Elastic Database Query.

Zie SQL [Database Pricing Details](https://azure.microsoft.com/pricing/details/sql-database/)voor prijsinformatie .

## <a name="next-steps"></a>Volgende stappen

* Zie Overzicht van elastische query's voor een overzicht van elastische [query's](sql-database-elastic-query-overview.md).
* Zie Aan de slag [met cross-databasequery (verticale partitionering)](sql-database-elastic-query-getting-started-vertical.md)voor een verticale zelfstudie voor het partitioneren.
* Zie [Verticaal verdeelde gegevens opvragen voor](sql-database-elastic-query-vertical-partitioning.md) syntaxis- en voorbeeldquery's voor verticaal verdeelde gegevens)
* Zie [Horizontaal gepartitioneerde gegevens opvragen voor](sql-database-elastic-query-horizontal-partitioning.md) syntaxis- en voorbeeldquery's voor horizontaal verdeelde gegevens)
* Zie [\_sp \_remote uitvoeren](https://msdn.microsoft.com/library/mt703714) voor een opgeslagen procedure die een Transact-SQL-instructie uitvoert op één externe Azure SQL-database of set databases die als shards dienen in een horizontaal partitioneringsschema.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
