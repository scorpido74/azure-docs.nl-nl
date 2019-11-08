---
title: Rapportage voor meerdere uitgeschaalde clouddatabases
description: Data base query's tussen meerdere data bases gebruiken om te rapporteren over verschillende data bases.
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823778"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Rapport over uitgeschaalde Cloud databases (preview-versie)

U kunt met behulp van een [elastische query](sql-database-elastic-query-overview.md)rapporten maken op basis van meerdere Azure SQL-data bases vanaf één verbindings punt. De data bases moeten horizon taal zijn gepartitioneerd (ook wel ' Shard ' genoemd).

Als u een bestaande Data Base hebt, raadpleegt u [bestaande data bases migreren naar uitgeschaalde data bases](sql-database-elastic-convert-to-use-elastic-tools.md).

Zie [query voor Horizon taal gepartitioneerde data bases](sql-database-elastic-query-horizontal-partitioning.md)voor meer informatie over de SQL-objecten die nodig zijn voor het uitvoeren van query's.

## <a name="prerequisites"></a>Vereisten

Down load en voer het [voor beeld aan de slag met Elastic database-hulpprogram ma's](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Een Shard-toewijzings beheer maken met behulp van de voor beeld-app
Hier maakt u een Shard-toewijzings beheer samen met verschillende Shards, gevolgd door het invoegen van gegevens in de Shards. Als er al een Shards-instelling is met Shard-gegevens, kunt u de volgende stappen overs Laan en door gaan naar de volgende sectie.

1. Bouw en voer de voorbeeld toepassing aan de slag **met Elastic database-hulpprogram ma's** uit door de stappen in de sectie artikel te volgen om [de voor beeld-app te downloaden en uit te voeren](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app-1). Nadat u alle stappen hebt voltooid, wordt de volgende opdracht prompt weer gegeven:

    ![opdracht prompt][1]
2. Typ ' 1 ' in het opdracht venster en druk op **Enter**. Hiermee wordt het Shard-toewijzings beheer gemaakt en worden twee Shards toegevoegd aan de server. Typ "3" en druk op **Enter**; Herhaal de actie vier keer. Hiermee worden de rijen met voorbeeld gegevens in uw Shards ingevoegd.
3. De [Azure Portal](https://portal.azure.com) moet drie nieuwe data bases in uw server weer geven:

   ![Visual Studio-bevestiging][2]

   Op dit moment worden query's tussen data bases ondersteund via de Elastic Database-client bibliotheken. Gebruik bijvoorbeeld optie 4 in het opdracht venster. De resultaten van een query met meerdere Shard zijn altijd een **samen voeging van alle** resultaten van alle Shards.

   In de volgende sectie maken we een voor beeld-data base-eind punt dat ondersteuning biedt voor rijkere query's van de gegevens in Shards.

## <a name="create-an-elastic-query-database"></a>Een elastische query database maken
1. Open de [Azure Portal](https://portal.azure.com) en meld u aan.
2. Maak een nieuwe Azure-SQL database op dezelfde server als de Shard-installatie. Geef de data base de naam ' ElasticDBQuery '.

    ![Azure Portal en prijs categorie][3]

    > [!NOTE]
    > u kunt een bestaande Data Base gebruiken. Als u dit wel kunt doen, mag dit niet een van de Shards zijn waarop u uw query's wilt uitvoeren. Deze data base wordt gebruikt voor het maken van de meta gegevens objecten voor een Elastic data base-query.
    >

## <a name="create-database-objects"></a>Database objecten maken
### <a name="database-scoped-master-key-and-credentials"></a>Data base-scoped hoofd sleutel en referenties
Deze worden gebruikt om verbinding te maken met de Shard-kaart Manager en de Shards:

1. Open SQL Server Management Studio of SQL Server Data Tools in Visual Studio.
2. Maak verbinding met de ElasticDBQuery-data base en voer de volgende T-SQL-opdrachten uit:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    gebruikers naam en wacht woord moeten hetzelfde zijn als de aanmeldings gegevens die worden gebruikt in stap 3 van [de sectie downloaden en de voor beeld-app uitvoeren](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app) in het artikel aan de slag **met Elastic database-hulpprogram ma's** .

### <a name="external-data-sources"></a>Externe gegevens bronnen
Als u een externe gegevens bron wilt maken, voert u de volgende opdracht uit op de ElasticDBQuery-Data Base:

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 ' CustomerIDShardMap ' is de naam van de Shard-toewijzing als u de Shard-kaart en het Shard-toewijzings beheer hebt gemaakt met behulp van het Elastic Data Base-voor beeld. Als u echter de aangepaste installatie voor dit voor beeld hebt gebruikt, moet dit de naam zijn van de Shard-kaart die u in uw toepassing hebt gekozen.

### <a name="external-tables"></a>Externe tabellen
Maak een externe tabel die overeenkomt met de tabel klanten op het Shards door de volgende opdracht uit te voeren in de ElasticDBQuery-Data Base:

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Een voor beeld van een Elastic data base-T-SQL-query uitvoeren
Wanneer u uw externe gegevens bron en uw externe tabellen hebt gedefinieerd, kunt u nu volledige T-SQL gebruiken voor uw externe tabellen.

Deze query uitvoeren op de ElasticDBQuery-Data Base:

    select count(CustomerId) from [dbo].[Customers]

U ziet dat de query de resultaten van alle Shards samenvoegt en de volgende uitvoer levert:

![Uitvoer Details][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Query resultaten voor Elastic data base importeren in Excel
 U kunt de resultaten van een query importeren in een Excel-bestand.

1. Start Excel 2013.
2. Navigeer naar het **gegevens** lint.
3. Klik **in andere bronnen** op en klik op **van SQL Server**.

   ![Excel importeren uit andere bronnen][5]
4. Typ in de **wizard gegevens verbinding** de server naam en aanmeldings referenties. Klik op **Volgende**.
5. **Selecteer de data base die de gewenste gegevens bevat**in het dialoog venster, selecteer de **ElasticDBQuery** -data base.
6. Selecteer de tabel **klanten** in de lijst weergave en klik op **volgende**. Klik vervolgens op **volt ooien**.
7. Selecteer in het formulier **gegevens importeren** onder **Selecteer hoe u deze gegevens wilt weer geven in uw werkmap** **tabel** en klik vervolgens op **OK**.

Alle rijen uit de tabel **klanten** , die zijn opgeslagen in verschillende Shards, vullen het Excel-werk blad.

U kunt nu de krachtige functies van gegevens visualisatie van Excel gebruiken. U kunt de connection string met de server naam, de database naam en de referenties gebruiken om uw BI-en gegevens integratie hulpprogramma's te verbinden met de Elastic query-data base. Zorg ervoor dat SQL Server wordt ondersteund als gegevens bron voor uw hulp programma. U kunt naar de elastische query database en externe tabellen verwijzen net als andere SQL Server Data Base-en SQL Server tabellen waarmee u verbinding met uw hulp programma zou maken.

### <a name="cost"></a>Kosten
Er worden geen extra kosten in rekening gebracht voor het gebruik van de functie voor Elastic Database Query's.

Zie [SQL database prijs](https://azure.microsoft.com/pricing/details/sql-database/)informatie voor prijzen.

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van elastische query's](sql-database-elastic-query-overview.md)voor een overzicht van elastische query's.
* Zie aan de slag [met query's tussen data bases (verticaal partitioneren)](sql-database-elastic-query-getting-started-vertical.md)voor een verticaal gepartitioneerde zelf studie.
* Zie query's [uitvoeren op verticaal gepartitioneerde gegevens](sql-database-elastic-query-vertical-partitioning.md) voor syntaxis-en voorbeeld query's voor verticaal gepartitioneerde gegevens)
* Zie query's [uitvoeren in horizon taal gepartitioneerde gegevens](sql-database-elastic-query-horizontal-partitioning.md) voor syntaxis-en voorbeeld query's voor Horizon taal gepartitioneerde gegevens)
* Zie [sp\_execute \_Remote](https://msdn.microsoft.com/library/mt703714) voor een opgeslagen procedure waarmee een Transact-SQL-instructie wordt uitgevoerd op één externe Azure SQL database of een set met data bases die fungeren als Shards in een horizon taal partitie schema.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
