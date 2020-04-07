---
title: Aanbevolen procedures voor het laden van gegevens voor Synapse SQL-pool
description: Aanbevelingen en prestatieoptimalisaties voor het laden van gegevens met Behulp van Synapse SQL-pool.
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
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745507"
---
# <a name="best-practices-for-loading-data-using-synapse-sql-pool"></a>Aanbevolen procedures voor het laden van gegevens met synapssql-pool

In dit artikel leert u aanbevelingen en prestatieoptimalisaties voor het laden van gegevens met SQL-pool.

## <a name="preparing-data-in-azure-storage"></a>Gegevens voorbereiden in Azure Storage

Als u de latentie wilt minimaliseren, moet u uw opslaglaag en uw SQL-groep co-lokaliseren.

Bij het exporteren van gegevens in een ORC-bestandsindeling kunnen er Java-geheugenfouten optreden wanneer er grote tekstkolommen zijn. U kunt deze beperking omzeilen door slechts een subset van de kolommen te exporteren.

PolyBase kan geen rijen laden met meer dan 1.000.000 bytes aan gegevens. Wanneer u gegevens in de tekstbestanden in Azure-blob-opslag of Azure Data Lake Store zet, moeten deze minder dan 1.000.000 bytes aan gegevens bevatten. Deze bytebeperking geldt ongeacht het tabelschema.

Alle bestandsindelingen hebben verschillende prestatiekenmerken. Gebruik voor het snelste laadproces gecomprimeerde tekstbestanden. Het verschil tussen UTF-8- en UTF-16-prestaties is minimaal.

Splits grote gecomprimeerde bestanden in kleinere gecomprimeerde bestanden.

## <a name="running-loads-with-enough-compute"></a>Laadtaken uitvoeren met voldoende rekenkracht

Voer voor de hoogste laadsnelheid slechts één taak tegelijk uit. Als dat niet haalbaar is, voert u een minimaal aantal ladingen tegelijk uit. Als u een grote laadtaak verwacht, u overwegen uw SQL-groep vóór de belasting op te schalen.

Als u loads wilt uitvoeren met geschikte rekenresources, maakt u gebruikers voor het laadproces die zijn aangewezen voor het uitvoeren van loads. Wijs elke ladende gebruiker toe aan een specifieke resourceklasse of werkbelastinggroep. Als u een belasting wilt uitvoeren, meldt u zich aan als een van de laadgebruikers en voert u de lading uit. De load wordt uitgevoerd met de resourceklasse van de gebruiker.  

> [!NOTE]
> Deze methode is eenvoudiger dan de resourceklasse van een gebruiker aanpassen om te voldoen aan de huidige benodigde resourceklasse.

### <a name="example-of-creating-a-loading-user"></a>Voorbeeld van het maken van een gebruiker voor het laadproces

In dit voorbeeld wordt een gebruiker voor het laadproces gemaakt voor de resourceklasse staticrc20. De eerste stap is **verbinding maken met de master** en een aanmelding maken.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

Maak verbinding met de SQL-groep en maak een gebruiker. In de volgende code wordt ervan uitgegaan dat u bent verbonden met de database genaamd mySampleDataWarehouse. Het laat zien hoe u een gebruiker met de naam LoaderRC20 maakt en geeft de gebruiker toestemming voor een database. Vervolgens voegt het de gebruiker toe als lid van de statische rc20-databaserol.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

Als u een belasting met resources wilt uitvoeren voor de statische RC20-resourceklassen, meldt u zich aan als LoaderRC20 en voert u de belasting uit.

Voer loads bij voorkeur uit onder statische en niet onder dynamische resourceklassen. Het gebruik van de statische resourceklassen garandeert dezelfde resources, ongeacht uw [gegevensmagazijneenheden.](what-is-a-data-warehouse-unit-dwu-cdwu.md) Als u een dynamische resourceklasse gebruikt, variëren de resources afhankelijk van uw serviceniveau.

Voor dynamische klassen betekent een lager serviceniveau dat u waarschijnlijk een grotere resourceklasse moet gebruiken voor uw gebruiker van het laadproces.

## <a name="allowing-multiple-users-to-load"></a>Meerdere gebruikers toestaan te laden

Er is vaak behoefte aan meerdere gebruikers die gegevens in een SQL-groep laden. Voor laden met de [TABEL MAKEN ALS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) zijn controlmachtigingen van de database vereist.  De CONTROL-machtiging biedt beheertoegang tot alle schema's.

Mogelijk wilt u niet alle gebruikers die laadtaken uitvoeren, beheertoegang tot alle schema's verlenen. Als u machtigingen wilt beperken, kunt u de instructie DENY CONTROL gebruiken.

Denk bijvoorbeeld aan databaseschema's, schema_A voor afdeling A, en schema_B voor afdeling B. Laat databasegebruikers gebruiker_A en gebruiker_B gebruikers zijn voor PolyBase die respectievelijk laden in afdeling A en B. Beide zijn voorzien van databasemachtigingen voor CONTROL. De makers van schema A en B vergrendelen nu hun schema's met DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A en user_B zijn nu buitengesloten van het schema van de andere dept.

## <a name="loading-to-a-staging-table"></a>Laden naar een faseringstabel

Als u de snelste laadsnelheid wilt bereiken voor het verplaatsen van gegevens naar een SQL-pooltabel, laadt u gegevens in een faseringstabel.  Definieer de faseringstabel als een heap en gebruik round-robin voor de distributieoptie.

Houd er rekening mee dat laden meestal een proces in twee stappen is waarin u eerst naar een faseringstabel wordt geladen en de gegevens vervolgens in een productie-SQL-pooltabel invoegt. Als de productietabel een hash-distributiepunt gebruikt, is de totale tijd voor het laden en invoegen mogelijk sneller als u de faseringstabel met de hash-distributie definieert.

Het laden naar de faseringstabel duurt langer, maar de tweede stap van het invoegen van de rijen in de productietabel leidt niet tot de verplaatsing van gegevens in de distributies.

## <a name="loading-to-a-columnstore-index"></a>Laden naar een columnstore-index

Columnstore-indexen vereisen grote hoeveelheden geheugen voor het comprimeren van gegevens tot hoogwaardige rijgroepen. Voor de beste compressie en efficiëntie van de index moet de columnstore-index maximaal 1.048.576 rijen in elke rijgroep comprimeren.

Bij geheugenbelasting kan het zijn dat de columnstore-index de maximale compressiesnelheden niet kan halen. Dit scenario, op zijn beurt, effecten query prestaties. Zie voor gedetailleerde informatie [Columnstore geheugenoptimalisaties](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Zorg dat de gebruiker van het laadproces voldoende geheugen heeft om maximale compressiesnelheden te bereiken. Gebruik hiervoor gebruikers voor het laadproces die lid zijn van een middelgrote of grote resourceklasse.
- Laad genoeg rijen om nieuwe rijgroepen volledig te vullen. Tijdens een bulksgewijze laadtaak worden elke 1.048.576 rijen rechtstreeks in de columnstore gecomprimeerd als een volledige rijgroep. Laadtaken met minder dan 102.400 rijen verzenden de rijen naar de deltastore waarin rijen zijn ondergebracht in een b-tree-index.

> [!NOTE]
> Als u te weinig rijen laadt, kunnen ze allemaal naar de deltastore worden gerouteerd en niet onmiddellijk worden gecomprimeerd in de indeling van de kolomopslag.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Batchgrootte vergroten bij gebruik van SqLBulkCopy API of bcp

Laden met PolyBase zorgt voor de hoogste doorvoer met SQL-pool. Als u PolyBase niet gebruiken om de [SqLBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) of [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)te laden en moet gebruiken, moet u overwegen de batchgrootte te vergroten voor een betere doorvoer.

> [!TIP]
> Een batchgrootte tussen 100 K en 1M rijen is de aanbevolen basislijn voor het bepalen van de optimale batchgroottecapaciteit.

## <a name="handling-loading-failures"></a>Afhandeling van fouten bij het laden

Een load met behulp van een externe tabel kan mislukken met de fout *Query afgebroken--de maximale weigeringsdrempelwaarde is bereikt tijdens het lezen vanuit een externe bron*. Dit bericht geeft aan dat uw externe gegevens vervuilde records bevatten.

Een gegevensrecord wordt als vuil beschouwd als deze voldoet aan een van de volgende voorwaarden:

- De gegevenstypen en het aantal kolommen komen niet overeen met de kolomdefinities van de externe tabel.
- De gegevens voldoen niet aan de opgegeven externe bestandsindeling.

U kunt vervuilde records voorkomen door ervoor te zorgen dat uw externe tabel- en bestandindelingsdefinities correct zijn en uw externe gegevens overeenstemmen met deze definities.

Als een subset van externe gegevensrecords vuil is, u ervoor kiezen deze records voor uw query's af te wijzen met behulp van de afwijsopties in [EXTERNE TABEL MAKEN (Transact-SQL).](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="inserting-data-into-a-production-table"></a>Gegevens in een productietabel invoegen

Een eenmalige laadtaak naar een kleine tabel met een [INSERT-instructie](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) of zelfs een periodieke herlaadtaak kan een acceptabel resultaat geven met een instructie zoals `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Het invoegen van singletons is echter niet zo efficiënt als bulksgewijs laden.

Als u de hele dag door duizenden of meerdere enkele gegevens wilt invoeren, voeg de gegevens dan samen tot een batch zodat deze bulksgewijs kunt laden.  Ontwikkel uw processen om de afzonderlijke gegevens aan een bestand toe te voegen en maak vervolgens een ander proces dat het bestand periodiek laadt.

## <a name="creating-statistics-after-the-load"></a>Statistieken maken na het laden

Voor optimale resultaten van uw query's is het belangrijk dat u statistieken maakt voor alle kolommen van alle tabellen nadat de gegevens voor het eerst zijn geladen of wanneer de gegevens substantieel zijn gewijzigd. Het maken van statistieken kan handmatig worden gedaan of u [AUTO_CREATE_STATISTICS](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic)inschakelen.

Zie [statistieken](sql-data-warehouse-tables-statistics.md) voor gedetailleerde uitleg van statistieken. In het volgende voorbeeld ziet u hoe u handmatig statistieken maakt over vijf kolommen van de tabel Customer_Speed.

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
