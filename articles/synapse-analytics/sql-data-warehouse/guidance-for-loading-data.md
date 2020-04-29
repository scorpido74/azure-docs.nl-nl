---
title: Aanbevolen procedures voor het laden van gegevens voor de SQL-groep Synapse
description: Aanbevelingen en prestatie optimalisaties voor het laden van gegevens met behulp van de Synapse SQL-pool.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e170a789727fb0de36705895245cc638d30ee3d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745507"
---
# <a name="best-practices-for-loading-data-using-synapse-sql-pool"></a>Aanbevolen procedures voor het laden van gegevens met behulp van de Synapse SQL-pool

In dit artikel leert u aanbevelingen en prestatie optimalisaties voor het laden van gegevens met behulp van de SQL-groep.

## <a name="preparing-data-in-azure-storage"></a>Gegevens voorbereiden in Azure Storage

Als u de latentie wilt minimaliseren, gaat u naar uw opslaglaag en uw SQL-groep.

Bij het exporteren van gegevens in een ORC-bestandsindeling kunnen er Java-geheugenfouten optreden wanneer er grote tekstkolommen zijn. U kunt deze beperking omzeilen door slechts een subset van de kolommen te exporteren.

Poly Base kan rijen met meer dan 1.000.000 bytes aan gegevens niet laden. Wanneer u gegevens in de tekstbestanden in Azure-blob-opslag of Azure Data Lake Store zet, moeten deze minder dan 1.000.000 bytes aan gegevens bevatten. Deze bytebeperking geldt ongeacht het tabelschema.

Alle bestandsindelingen hebben verschillende prestatiekenmerken. Gebruik voor het snelste laadproces gecomprimeerde tekstbestanden. Het verschil tussen UTF-8- en UTF-16-prestaties is minimaal.

Splits grote gecomprimeerde bestanden in kleinere gecomprimeerde bestanden.

## <a name="running-loads-with-enough-compute"></a>Laadtaken uitvoeren met voldoende rekenkracht

Voer voor de hoogste laadsnelheid slechts één taak tegelijk uit. Als dat niet het geval is, voert u tegelijkertijd een mini maal aantal loads uit. Als u een grote laad taak verwacht, kunt u de SQL-groep vóór de belasting verg Roten of verkleinen.

Als u loads wilt uitvoeren met geschikte rekenresources, maakt u gebruikers voor het laadproces die zijn aangewezen voor het uitvoeren van loads. Wijs elke laad gebruiker toe aan een specifieke resource klasse of werkbelasting groep. Als u een belasting wilt uitvoeren, meldt u zich aan als een van de laad gebruikers en voert u de belasting uit. De load wordt uitgevoerd met de resourceklasse van de gebruiker.  

> [!NOTE]
> Deze methode is eenvoudiger dan de resourceklasse van een gebruiker aanpassen om te voldoen aan de huidige benodigde resourceklasse.

### <a name="example-of-creating-a-loading-user"></a>Voorbeeld van het maken van een gebruiker voor het laadproces

In dit voorbeeld wordt een gebruiker voor het laadproces gemaakt voor de resourceklasse staticrc20. De eerste stap is **verbinding maken met de master** en een aanmelding maken.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

Verbinding maken met de SQL-groep en een gebruiker aanmaken. In de volgende code wordt ervan uitgegaan dat u bent verbonden met de data base met de naam mySampleDataWarehouse. Hierin ziet u hoe u een gebruiker met de naam LoaderRC20 maakt en de gebruiker controle machtigingen geeft voor een Data Base. Vervolgens wordt de gebruiker toegevoegd als lid van de databaserol staticrc20.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

Als u een belasting wilt uitvoeren met resources voor de resource klassen staticRC20, meldt u zich aan als LoaderRC20 en voert u de belasting uit.

Voer loads bij voorkeur uit onder statische en niet onder dynamische resourceklassen. Het gebruik van de statische resource klassen garandeert dezelfde bronnen, ongeacht uw [Data Warehouse-eenheden](what-is-a-data-warehouse-unit-dwu-cdwu.md). Als u een dynamische resourceklasse gebruikt, variëren de resources afhankelijk van uw serviceniveau.

Voor dynamische klassen betekent een lager serviceniveau dat u waarschijnlijk een grotere resourceklasse moet gebruiken voor uw gebruiker van het laadproces.

## <a name="allowing-multiple-users-to-load"></a>Meerdere gebruikers toestaan te laden

Er zijn vaak meerdere gebruikers nodig om gegevens in een SQL-groep te laden. Bij het laden met de [Create Table als Select (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) zijn machtigingen vereist van de data base.  De CONTROL-machtiging biedt beheertoegang tot alle schema's.

Mogelijk wilt u niet alle gebruikers die laadtaken uitvoeren, beheertoegang tot alle schema's verlenen. Als u machtigingen wilt beperken, kunt u de instructie DENY CONTROL gebruiken.

Denk bijvoorbeeld aan databaseschema's, schema_A voor afdeling A, en schema_B voor afdeling B. Laat databasegebruikers gebruiker_A en gebruiker_B gebruikers zijn voor PolyBase die respectievelijk laden in afdeling A en B. Beide zijn voorzien van databasemachtigingen voor CONTROL. De makers van schema A en B vergrendelen nu hun schema's met DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A en user_B zijn nu uitgesloten van het schema van de andere afdeling.

## <a name="loading-to-a-staging-table"></a>Laden naar een faseringstabel

Als u de snelste laad snelheid voor het verplaatsen van gegevens naar een SQL-groeps tabel wilt verzorgen, laadt u gegevens in een faserings tabel.  Definieer de faseringstabel als een heap en gebruik round-robin voor de distributieoptie.

Houd er rekening mee dat laden meestal een proces in twee stappen is waarin u eerst naar een faserings tabel laadt en vervolgens de gegevens invoegt in een tabel van een SQL-groep voor productie. Als de productietabel een hash-distributiepunt gebruikt, is de totale tijd voor het laden en invoegen mogelijk sneller als u de faseringstabel met de hash-distributie definieert.

Het laden naar de faseringstabel duurt langer, maar de tweede stap van het invoegen van de rijen in de productietabel leidt niet tot de verplaatsing van gegevens in de distributies.

## <a name="loading-to-a-columnstore-index"></a>Laden naar een columnstore-index

Columnstore-indexen vereisen grote hoeveelheden geheugen voor het comprimeren van gegevens tot hoogwaardige rijgroepen. Voor de beste compressie en efficiëntie van de index moet de columnstore-index maximaal 1.048.576 rijen in elke rijgroep comprimeren.

Bij geheugenbelasting kan het zijn dat de columnstore-index de maximale compressiesnelheden niet kan halen. Dit scenario heeft invloed op de prestaties van query's. Zie voor gedetailleerde informatie [Columnstore geheugenoptimalisaties](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Zorg dat de gebruiker van het laadproces voldoende geheugen heeft om maximale compressiesnelheden te bereiken. Gebruik hiervoor gebruikers voor het laadproces die lid zijn van een middelgrote of grote resourceklasse.
- Laad genoeg rijen om nieuwe rijgroepen volledig te vullen. Tijdens een bulksgewijze laadtaak worden elke 1.048.576 rijen rechtstreeks in de columnstore gecomprimeerd als een volledige rijgroep. Laadtaken met minder dan 102.400 rijen verzenden de rijen naar de deltastore waarin rijen zijn ondergebracht in een b-tree-index.

> [!NOTE]
> Als u te weinig rijen laadt, kunnen ze alle routes naar de Delta Store en worden ze niet onmiddellijk gecomprimeerd naar de column Store-indeling.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Batch grootte verg Roten bij gebruik van SqLBulkCopy-API of BCP

Het laden met poly Base biedt de hoogste door Voer met SQL-groep. Als u geen poly Base kunt gebruiken om te laden en u de [SqLBulkCopy-API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) of [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)moet gebruiken, kunt u overwegen om de Batch grootte te verg Roten voor een betere door voer.

> [!TIP]
> Een batch grootte tussen 100 K en 1M rijen is de aanbevolen basis lijn voor het bepalen van de optimale grootte van de batch capaciteit.

## <a name="handling-loading-failures"></a>Afhandeling van fouten bij het laden

Een load met behulp van een externe tabel kan mislukken met de fout *Query afgebroken--de maximale weigeringsdrempelwaarde is bereikt tijdens het lezen vanuit een externe bron*. Dit bericht geeft aan dat uw externe gegevens vervuilde records bevatten.

Een gegevens record wordt als vuil beschouwd als deze voldoet aan een van de volgende voor waarden:

- De gegevens typen en het aantal kolommen komen niet overeen met de kolom definities van de externe tabel.
- De gegevens voldoen niet aan de opgegeven externe bestands indeling.

U kunt vervuilde records voorkomen door ervoor te zorgen dat uw externe tabel- en bestandindelingsdefinities correct zijn en uw externe gegevens overeenstemmen met deze definities.

Als een subset van externe gegevens records ongeldig is, kunt u ervoor kiezen deze records voor uw query's af te wijzen met behulp van de afwijzings opties in [Create External Table (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="inserting-data-into-a-production-table"></a>Gegevens in een productietabel invoegen

Een eenmalige laadtaak naar een kleine tabel met een [INSERT-instructie](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) of zelfs een periodieke herlaadtaak kan een acceptabel resultaat geven met een instructie zoals `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Het invoegen van singletons is echter niet zo efficiënt als bulksgewijs laden.

Als u de hele dag door duizenden of meerdere enkele gegevens wilt invoeren, voeg de gegevens dan samen tot een batch zodat deze bulksgewijs kunt laden.  Ontwikkel uw processen om de afzonderlijke gegevens aan een bestand toe te voegen en maak vervolgens een ander proces dat het bestand periodiek laadt.

## <a name="creating-statistics-after-the-load"></a>Statistieken maken na het laden

Voor optimale resultaten van uw query's is het belangrijk dat u statistieken maakt voor alle kolommen van alle tabellen nadat de gegevens voor het eerst zijn geladen of wanneer de gegevens substantieel zijn gewijzigd. Het maken van statistieken kan hand matig worden uitgevoerd of u kunt [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic)inschakelen.

Zie [statistieken](sql-data-warehouse-tables-statistics.md) voor gedetailleerde uitleg van statistieken. In het volgende voor beeld ziet u hoe u hand matig statistieken maakt voor vijf kolommen van de tabel Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Opslagsleutels draaien

Het is verstandig uit veiligheidsoverwegingen de toegangssleutel in de blob-opslag regelmatig te wijzigen. Er zijn twee opslagsleutels voor uw blob-opslagaccount, waarmee u de sleutels kunt wijzigen.

Sleutels van het Microsoft Azure Storage-account draaien:

Voor elk opslagaccount waarvan de sleutel is gewijzigd, moet u [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) uitvoeren.

Voorbeeld:

De oorspronkelijke sleutel wordt gemaakt

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

Rotate key from key 1 to key 2

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

Er hoeven geen andere wijzigingen te worden aangebracht aan onderliggende externe gegevensbronnen.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over PolyBase en het ontwerpen van een Extract, Load en Transform (ELT)-proces [ELT ontwerpen voor SQL Data Warehouse](design-elt-data-loading.md).
- Gebruik voor het laden van een zelfstudie [PolyBase om gegevens te laden uit Azure blob-opslag naar Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).
- Zie [Uw workload controleren met DMV's](sql-data-warehouse-manage-monitor.md) voor het controleren van het laden van gegevens.
