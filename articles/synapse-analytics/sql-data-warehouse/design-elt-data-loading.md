---
title: Ontwerp ELT in plaats van ETL
description: Flexibele strategieën voor het laden van gegevens implementeren voor Synapse SQL-groep binnen Azure Synapse Analytics
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/13/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 1b73b82b4367d50cc5fbe9881a67e0afa041db86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201155"
---
# <a name="data-loading-strategies-for-synapse-sql-pool"></a>Strategieën voor het laden van gegevens voor een Synapse SQL-pool

Traditionele SMP-SQL-Pools gebruiken een proces voor het laden van de gegevens extractie, transformeren en laden (ETL). Synapse SQL-pool, in azure Synapse Analytics, heeft een enorm parallelle verwerkings architectuur (MPP) die gebruikmaakt van de schaal baarheid en flexibiliteit van reken-en opslag resources.

Het gebruik van een proces voor extractie, laden en transformeren (ELT) maakt gebruik van MPP en elimineert de resources die nodig zijn voor gegevens transformatie voordat ze worden geladen.

Hoewel de SQL-groep veel methoden voor het laden ondersteunt, waaronder populaire SQL Server opties zoals [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) en de [SqlBulkCopy-API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), is de snelste en meest schaal bare manier om gegevens te laden via Poly base externe tabellen en de [instructie Copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (preview).

Met poly base en de instructie COPY kunt u toegang krijgen tot externe gegevens die zijn opgeslagen in Azure Blob Storage of Azure Data Lake Store via de T-SQL-taal. Voor de meeste flexibiliteit bij het laden wordt u aangeraden de instructie COPY te gebruiken.

> [!NOTE]  
> De instructie COPY is momenteel beschikbaar als open bare preview. Als u feedback wilt geven, verzendt u een e-mail naar de volgende distributie lijst: sqldwcopypreview@service.microsoft.com .

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>Wat is ELT?

Extra heren, laden en transformeren (ELT) is een proces waarmee gegevens worden geëxtraheerd uit een bron systeem, in een SQL-groep worden geladen en vervolgens worden getransformeerd.

De basis stappen voor het implementeren van ELT zijn:

1. Extraheer de brongegevens naar tekstbestanden.
2. Voer de gegevens in op Azure Blob-opslag of Azure Data Lake Store.
3. De gegevens voorbereiden voor het laden van.
4. Laad de gegevens in faserings tabellen met poly base of de Kopieer opdracht.
5. Transformeer de gegevens.
6. Voeg de gegevens in productietabellen in.

Zie [gegevens laden uit Azure Blob-opslag](load-data-from-azure-blob-storage-using-polybase.md)voor een zelf studie over het laden van.

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extraheer de bron gegevens in tekst bestanden

Het ophalen van gegevens uit uw bron systeem is afhankelijk van de opslag locatie. Het doel is om de gegevens te verplaatsen naar ondersteunde gescheiden tekst of CSV-bestanden.

### <a name="supported-file-formats"></a>Ondersteunde bestandsindelingen

Met poly base en de instructie COPY kunt u gegevens laden van UTF-8-en UTF-16-gecodeerde tekst of CSV-bestanden met scheidings tekens. Behalve tekst-of CSV-bestanden, worden ze geladen vanuit de Hadoop-bestands indelingen, zoals ORC en Parquet. Poly base en de instructie COPY kunnen ook gegevens laden uit gzip-en Snappy gecomprimeerde bestanden.

Uitgebreide ASCII-indeling met een vaste breedte en geneste indelingen, zoals WinZip of XML, worden niet ondersteund. Als u exporteert vanaf SQL Server, kunt u het [opdracht regel programma BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) gebruiken om de gegevens te exporteren naar tekst bestanden met scheidings tekens.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Voer de gegevens over naar Azure Blob-opslag of Azure Data Lake Store

Als u de gegevens in azure Storage wilt laten staan, kunt u deze verplaatsen naar [Azure Blob-opslag](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) of [Azure data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). In beide locaties moeten de gegevens worden opgeslagen in tekst bestanden. Poly base en de instructie COPY kunnen worden geladen vanuit een van beide locaties.

Hulpprogram ma's en services die u kunt gebruiken om gegevens naar Azure Storage te verplaatsen:

- De [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) -service verbetert de netwerk doorvoer, prestaties en voorspel baarheid. ExpressRoute is een service waarmee uw gegevens worden gerouteerd via een speciale particuliere verbinding met Azure. ExpressRoute-verbindingen sturen geen gegevens via het open bare Internet. De verbindingen bieden meer betrouw baarheid, hogere snelheden, lagere wacht tijden en hogere beveiliging dan typische verbindingen via het open bare Internet.
- Met het [hulp programma AZCopy](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) kunt u gegevens verplaatsen naar Azure Storage via het open bare Internet. Dit werkt als uw gegevens grootten minder dan 10 TB zijn. Als u de belasting regel matig wilt uitvoeren met AZCopy, test u de netwerk snelheid om te controleren of deze acceptabel is.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) heeft een gateway die u op uw lokale server kunt installeren. Vervolgens kunt u een pijp lijn maken om gegevens van uw lokale server naar Azure Storage te verplaatsen. Zie [gegevens laden voor SQL-groep](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)om Data Factory met SQL-pool te gebruiken.

## <a name="3-prepare-the-data-for-loading"></a>3. de gegevens voorbereiden voor het laden

Mogelijk moet u de gegevens in uw opslag account voorbereiden en opschonen voordat u ze laadt. Gegevens voorbereiding kan worden uitgevoerd terwijl de gegevens zich in de bron bevinden, terwijl u de gegevens naar tekst bestanden exporteert of wanneer de gegevens zich in Azure Storage bevinden.  Het is eenvoudig om zo snel mogelijk in het proces te werken met de gegevens.  

### <a name="define-the-tables"></a>De tabellen definiëren

U moet eerst de tabel (len) die u in de SQL-groep laadt, definiëren wanneer u de instructie COPY gebruikt.

Als u poly base gebruikt, moet u externe tabellen definiëren in uw SQL-groep voordat u ze laadt. Poly base gebruikt externe tabellen voor het definiëren en openen van de gegevens in Azure Storage. Een externe tabel is vergelijkbaar met een database weergave. De externe tabel bevat het tabel schema en verwijst naar gegevens die buiten de SQL-groep zijn opgeslagen.

Als u externe tabellen definieert, moet u de gegevens bron, de indeling van de tekst bestanden en de tabel definities opgeven. Naslag informatie over T-SQL-syntaxis die u nodig hebt, zijn:

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Gebruik de volgende SQL-gegevens type toewijzing bij het laden van Parquet-bestanden:

|                         Type Parquet                         |   Parquet logische type (annotatie)   |  SQL-gegevenstype   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           True                            |                                       |       bit        |
|                     BINARY/BYTE_ARRAY                      |                                       |    varbinary     |
|                            DUBBELKLIK                            |                                       |      float       |
|                            FLOAT                             |                                       |       werkelijk       |
|                            INT32                             |                                       |       int        |
|                            INT64                             |                                       |      bigint      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      binair      |
|                            WAARDE                            |                 UTF8                  |     nvarchar     |
|                            WAARDE                            |                TEKENREEKSEXPRESSIE                 |     nvarchar     |
|                            WAARDE                            |                 VASTE                  |     nvarchar     |
|                            WAARDE                            |                 MEE                  | uniqueidentifier |
|                            WAARDE                            |                KOMMA                |     decimal      |
|                            WAARDE                            |                 JSON                  |  nvarchar(MAX)   |
|                            WAARDE                            |                 BSON                  |  varbinary (max)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                KOMMA                |     decimal      |
|                          BYTE_ARRAY                          |               BEREIK                |  varchar (max),   |
|                            INT32                             |             INT (8, True)              |     smallint     |
|                            INT32                             |            INT (16, True)            |     smallint     |
|                            INT32                             |             INT (32, True)             |       int        |
|                            INT32                             |            INT (8, false)            |     tinyint      |
|                            INT32                             |            INT (16, false)             |       int        |
|                            INT32                             |           INT (32, false)            |      bigint      |
|                            INT32                             |                 DATE                  |       datum       |
|                            INT32                             |                KOMMA                |     decimal      |
|                            INT32                             |            TIJD (MILLIS)             |       tijd       |
|                            INT64                             |            INT (64, True)            |      bigint      |
|                            INT64                             |           INT (64, false)            |  decimaal (20, 0)   |
|                            INT64                             |                KOMMA                |     decimal      |
|                            INT64                             |         TIJD (MICRON/NANOS)         |       tijd       |
|                            INT64                             | TIJDS TEMPEL (MILLIS/MICRON/NANOS) |    datetime2     |
| [Complex type](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 ORDERVERZAMELLIJST                  |   varchar(max)   |
| [Complex type](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  DIAGRAM                  |   varchar(max)   |



Zie voor een voor beeld van het maken van externe objecten [externe tabellen maken](https://docs.microsoft.com/azure/synapse-analytics/sql/develop-tables-external-tables?tabs=sql-pool).

### <a name="format-text-files"></a>Tekst bestanden opmaken

Als u poly base gebruikt, moeten de gedefinieerde externe objecten de rijen van de tekst bestanden uitlijnen met de definitie van de externe tabel en de bestands indeling. De gegevens in elke rij van het tekst bestand moeten worden uitgelijnd met de tabel definitie.
De tekst bestanden opmaken:

- Als uw gegevens afkomstig zijn uit een niet-relationele bron, moet u deze omzetten in rijen en kolommen. Of de gegevens afkomstig zijn uit een relationele of niet-relationele bron, dat de gegevens moeten worden getransformeerd om te worden uitgelijnd met de kolom definities voor de tabel waarin u de gegevens wilt laden.
- Gegevens in het tekst bestand opmaken om uit te lijnen met de kolommen en gegevens typen in de doel tabel. Bij een onjuiste uitlijning tussen gegevens typen in de externe tekst bestanden en de tabel met SQL-groepen worden de rijen tijdens de belasting geweigerd.
- Scheid velden in het tekst bestand met een terminator.  Zorg ervoor dat u een teken of een teken reeks gebruikt die niet in de bron gegevens voor komt. Gebruik de Terminator die u hebt opgegeven met [externe BESTANDS indeling maken](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. gegevens laden met poly base of de instructie COPY

Het is best practice om gegevens in een faserings tabel te laden. Met faserings tabellen kunt u fouten afhandelen zonder de productie tabellen te verstoren. Een faserings tabel biedt u ook de mogelijkheid om de parallelle verwerkings architectuur van de SQL-groep te gebruiken voor gegevens transformaties voordat u de gegevens in productie tabellen invoegt.

### <a name="options-for-loading"></a>Opties voor het laden van

Als u gegevens wilt laden, kunt u een van de volgende laad opties gebruiken:

- De [instructie Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) is het aanbevolen laad hulpprogramma waarmee u gegevens naadloos en flexibel kunt laden. De instructie bevat veel extra laad mogelijkheden die poly Base niet biedt. 
- [Poly Base met T-SQL](load-data-from-azure-blob-storage-using-polybase.md) vereist dat u externe gegevens objecten definieert.
- [Poly base-en copy-instructie met Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) is een andere Orchestration-tool.  Hierin worden een pijp lijn en plannings taken gedefinieerd.
- [Poly Base met SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) werkt goed als uw bron gegevens zich in SQL Server bevinden. SSIS definieert de bron-naar-doel tabel toewijzingen en organiseert ook de belasting. Als u al SSIS-pakketten hebt, kunt u de pakketten wijzigen om met de nieuwe Data Warehouse-bestemming te werken.
- [Poly Base met Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) gegevens overdraagt van een tabel naar een Databricks data frame en/of schrijft gegevens van een Databricks data frame naar een tabel met poly base.

### <a name="other-loading-options"></a>Andere opties voor laden

Naast poly base en de instructie COPY kunt u gebruikmaken van [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) of de SQLBULKCOPY- [API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). BCP wordt rechtstreeks naar de data base geladen zonder Azure Blob-opslag te passeren en is uitsluitend bedoeld voor kleine belastingen.

> [!NOTE]
> De belasting prestaties van deze opties zijn lager dan poly base en de instructie COPY.

## <a name="5-transform-the-data"></a>5. de gegevens transformeren

Terwijl de gegevens zich in de faserings tabel bevindt, voert u trans formaties uit die nodig zijn voor uw werk belasting. Verplaats de gegevens vervolgens naar een productie tabel.

## <a name="6-insert-the-data-into-production-tables"></a>6. Voeg de gegevens in productie tabellen in

INVOEGEN in... Met de instructie SELECT worden de gegevens van de faserings tabel naar de permanente tabel verplaatst.

Probeer tijdens het ontwerpen van een ETL-proces het proces uit te voeren op een kleine steek proef. Probeer 1000 rijen uit de tabel naar een bestand te halen, verplaats het naar Azure en probeer het te laden in een faserings tabel.

## <a name="partner-loading-solutions"></a>Oplossingen voor het laden van partners

Veel van onze partners hebben het laden van oplossingen. Zie een lijst met onze [oplossings partners](sql-data-warehouse-partner-business-intelligence.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie voor hulp bij het laden [Richtlijnen voor het laden van gegevens](guidance-for-loading-data.md).
