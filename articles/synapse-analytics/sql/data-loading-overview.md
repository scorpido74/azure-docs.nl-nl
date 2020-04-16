---
title: In plaats van ETL ontwerpt u ELT voor Synapse SQL-pool | Microsoft Documenten
description: In plaats van ETL ontwerp je een ELT-proces (Extract, Load en Transform) voor het laden van gegevens of SQL-pool.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 596f4bcf2e3f829430fdc90eb1806a44a84b2bc5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429588"
---
# <a name="designing-a-polybase-data-loading-strategy-for-azure-synapse-sql-pool"></a>Een PolyBase-strategie voor het laden van gegevens ontwerpen voor Azure Synapse SQL-pool

Traditionele SMP-gegevensmagazijnen gebruiken een ETL-proces (Extract, Transform and Load) voor het laden van gegevens. Azure SQL-pool is een enorm parallelle verwerkingsarchitectuur (MPP) die profiteert van de schaalbaarheid en flexibiliteit van reken- en opslagbronnen. Gebruikmakend van een EXTRACT, Load, and Transform (ELT) proces kan profiteren van MPP en resources elimineren die nodig zijn om de gegevens te transformeren voordat ze worden geladen.

Hoewel SQL-pool veel laadmethoden ondersteunt, waaronder niet-Polybase-opties zoals BCP en SQL BulkCopy API, is de snelste en meest schaalbare manier om te laden via PolyBase.  PolyBase is een technologie die via de T-SQL-taal toegang heeft tot externe gegevens die zijn opgeslagen in Azure Blob-opslag of Azure Data Lake Store.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>Wat is ELT?

Extract, Load, and Transform (ELT) is een proces waarbij gegevens worden geëxtraheerd uit een bronsysteem, in een datawarehouse worden geladen en vervolgens worden getransformeerd.

De basisstappen voor het implementeren van een PolyBase ELT voor SQL-groep zijn:

1. Extraheer de brongegevens naar tekstbestanden.
2. Land de gegevens in Azure Blob-opslag of Azure Data Lake Store.
3. Bereid de gegevens voor op het laden.
4. Laad de gegevens in SQL-poolfaseringstabellen met PolyBase.
5. Transformeer de gegevens.
6. Voeg de gegevens in productietabellen in.

Zie [PolyBase gebruiken om gegevens uit Azure blob-opslag te laden naar Azure SQL Data Warehouse voor](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)een zelfstudie voor het laden.

Zie [Patronen blog laden voor](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)meer informatie.

## <a name="1-extract-the-source-data-into-text-files"></a>1. De brongegevens extraheren in tekstbestanden

Het verkrijgen van gegevens uit uw bronsysteem is afhankelijk van de opslaglocatie.  Het doel is om de gegevens te verplaatsen naar PolyBase ondersteunde afgebakende tekstbestanden.

### <a name="polybase-external-file-formats"></a>Externe bestandsindelingen van PolyBase

PolyBase laadt gegevens van UTF-8 en UTF-16 gecodeerde gedelimited text files. Naast de afgebakende tekstbestanden wordt deze geladen vanuit de Hadoop-bestandsindelingen RC File, ORC en Parket. PolyBase kan ook gegevens laden van Gzip- en Snappy-gecomprimeerde bestanden. PolyBase biedt momenteel geen ondersteuning voor uitgebreide ASCII, een indeling met een vaste breedte en geneste indelingen zoals WinZip, JSON en XML.

Als u exporteert vanuit SQL Server, u [het opdrachtregelgereedschap bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) gebruiken om de gegevens te exporteren naar afgebakende tekstbestanden. De toewijzing van het parquet naar SQL DW-gegevenstype is de volgende:

| **Gegevenstype parket** |                      **SQL-gegevenstype**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        booleaans        |                             bit                              |
|        double         |                            float                             |
|         float         |                             real                             |
|        double         |                            Geld                             |
|        double         |                          kleingeld                          |
|        tekenreeks         |                            Nchar                             |
|        tekenreeks         |                           nvarchar                           |
|        tekenreeks         |                             Char                             |
|        tekenreeks         |                           varchar                            |
|        binair         |                            binair                            |
|        binair         |                          varbinary varbinary                           |
|       tijdstempel       |                             date                             |
|       tijdstempel       |                        smalldatetijd                         |
|       tijdstempel       |                          datetime2                           |
|       tijdstempel       |                           datum/tijd                           |
|       tijdstempel       |                             tijd                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. De gegevens in Azure Blob-opslag of Azure Data Lake Store verwerken

Als u de gegevens wilt verwerken in Azure-opslag, u deze verplaatsen naar [Azure Blob-opslag](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) of [Azure Data Lake Store.](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) Op beide locaties moeten de gegevens worden opgeslagen in tekstbestanden. PolyBase kan vanaf beide locaties laden.

Hulpprogramma's en services die u gebruiken om gegevens naar Azure Storage te verplaatsen:

- [Azure ExpressRoute-service](../../expressroute/expressroute-introduction.md) verbetert de netwerkdoorvoer, -prestaties en -voorspelbaarheid. ExpressRoute is een service die uw gegevens via een speciale privéverbinding naar Azure leidt. ExpressRoute-verbindingen leiden gegevens niet via het openbare internet. De verbindingen bieden meer betrouwbaarheid, hogere snelheden, lagere latencies en hogere beveiliging dan typische verbindingen via het openbare internet.
- [AZCopy-hulpprogramma](../../storage/common/storage-use-azcopy-v10.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) verplaatst gegevens naar Azure Storage via het openbare internet. Dit werkt als uw gegevensgrootte minder dan 10 TB beslaat. Om regelmatig ladingen uit te voeren met AZCopy, test je de netwerksnelheid om te zien of deze acceptabel is.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) heeft een gateway die u op uw lokale server installeren. Vervolgens u een pijplijn maken om gegevens van uw lokale server naar Azure Storage te verplaatsen. Zie [Gegevens laden in SQL-groep](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)als u Gegevensfabriek wilt gebruiken met SQL-groep.

## <a name="3-prepare-the-data-for-loading"></a>3. De gegevens voorbereiden op het laden

Mogelijk moet u de gegevens in uw opslagaccount voorbereiden en schoonmaken voordat u deze in sql-groep laadt. Gegevensvoorbereiding kan worden uitgevoerd terwijl uw gegevens in de bron zijn, terwijl u de gegevens exporteert naar tekstbestanden of nadat de gegevens zich in Azure Storage bevinden.  Het is het gemakkelijkst om zo vroeg mogelijk met de gegevens te werken.  

### <a name="define-external-tables"></a>Externe tabellen definiëren

Voordat u gegevens laden, moet u externe tabellen in uw gegevensmagazijn definiëren. PolyBase gebruikt externe tabellen om de gegevens in Azure Storage te definiëren en te openen. Een externe tabel is vergelijkbaar met een databaseweergave. De externe tabel bevat het tabelschema en wijst naar gegevens die buiten het gegevensmagazijn zijn opgeslagen.

Het definiëren van externe tabellen omvat het opgeven van de gegevensbron, de indeling van de tekstbestanden en de tabeldefinities. Dit zijn de T-SQL syntax onderwerpen die u nodig hebt:

- [EXTERNE GEGEVENSBRON MAKEN](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [EXTERNE TABEL MAKEN](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

Zie de stap Externe tabellen [maken](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-external-tables-for-the-sample-data) in de bewerkvoorbereidingshandleiding voor een voorbeeld van het maken van externe objecten.

### <a name="format-text-files"></a>Tekstbestanden opmaken

Zodra de externe objecten zijn gedefinieerd, moet u de rijen van de tekstbestanden uitlijnen met de externe tabel en de definitie van bestandsindeling. De gegevens in elke rij van het tekstbestand moeten worden uitgelijnd met de tabeldefinitie.
Ga als u de tekstbestanden opmaak:

- Als uw gegevens afkomstig zijn van een niet-relationele bron, moet u deze omzetten in rijen en kolommen. Of de gegevens nu afkomstig zijn van een relationele of niet-relationele bron, de gegevens moeten worden getransformeerd om af te stemmen op de kolomdefinities voor de tabel waarin u van plan bent de gegevens te laden.
- Gegevens in het tekstbestand opmaken om uit te lijnen met de kolommen en gegevenstypen in de doeltabel SQL-groep. Verkeerde uitlijning tussen gegevenstypen in de externe tekstbestanden en de tabel met gegevensmagazijnen zorgt ervoor dat rijen tijdens de belasting worden geweigerd.
- Afzonderlijke velden in het tekstbestand met een terminator.  Zorg ervoor dat u een teken of een tekenreeks gebruikt die niet in uw brongegevens wordt gevonden. Gebruik de terminator die u hebt opgegeven met [EXTERNE BESTANDSINDELING MAKEN](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-into-sql-pool-staging-tables-using-polybase"></a>4. De gegevens in SQL-poolfaseringstabellen laden met PolyBase

Het is de beste gewoonte om gegevens in een faseringstabel te laden. Met faseringstabellen u fouten afhandelen zonder de productietabellen te verstoren. Een faseringstabel biedt u ook de mogelijkheid om SQL Pool MPP te gebruiken voor gegevenstransformaties voordat u de gegevens in productietabellen invoegt.

### <a name="options-for-loading-with-polybase"></a>Opties voor laden met PolyBase

Als u gegevens wilt laden met PolyBase, u een van de volgende laadopties gebruiken:

- [PolyBase met T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) werkt goed wanneer uw gegevens zich in Azure Blob-opslag of Azure Data Lake Store bevinden. Het geeft u de meeste controle over het laadproces, maar vereist ook dat u externe gegevensobjecten definieert. De andere methoden definiëren deze objecten achter de schermen terwijl u brontabellen naar doeltabellen mapt.  Als u T-SQL-belastingen wilt orkestreren, u Azure Data Factory-, SSIS- of Azure-functies gebruiken.
- [PolyBase met SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) werkt goed wanneer uw brongegevens zich in SQL Server bevinden, sql server on-premises of in de cloud. SSIS definieert de bron voor toewijzingen van doeltabeltoewijzingen en orkestreert ook de belasting. Als u al SSIS-pakketten hebt, u de pakketten aanpassen om te werken met de nieuwe bestemming voor gegevensmagazijnen.
- [PolyBase met Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) is een ander orchestration-hulpmiddel.  Het definieert een pijplijn en plant taken.
- [PolyBase met Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) brengt gegevens van een SQL Data Warehouse-tabel over naar een Databricks-gegevensframe en/of schrijft gegevens van een Databricks-gegevensframe naar een SQL Data Warehouse-tabel met PolyBase.

### <a name="non-polybase-loading-options"></a>Niet-PolyBase-laadopties

Als uw gegevens niet compatibel zijn met PolyBase, u [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) of de [SQLBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)gebruiken. bcp wordt rechtstreeks naar SQL-pool geladen zonder azure blob-opslag te doorlopen en is alleen bedoeld voor kleine belastingen. Let op, de laadprestaties van deze opties zijn aanzienlijk trager dan PolyBase.

## <a name="5-transform-the-data"></a>5. De gegevens transformeren

Terwijl gegevens zich in de faseringstabel bevinden, voert u transformaties uit die uw werkbelasting vereist. Verplaats de gegevens vervolgens naar een productietabel.

## <a name="6-insert-the-data-into-production-tables"></a>6. De gegevens in productietabellen invoegen

De INSERT IN ... Select-instructie verplaatst de gegevens van de faseringstabel naar de permanente tabel.

Terwijl u een ETL-proces ontwerpt, probeert u het proces uit te voeren op een klein testvoorbeeld. Probeer 1000 rijen uit de tabel naar een bestand te halen, verplaats het naar Azure en probeer het vervolgens in een faseringstabel te laden.

## <a name="partner-loading-solutions"></a>Oplossingen voor het laden van partners

Veel van onze partners hebben laadoplossingen. Zie voor meer informatie een lijst van onze [oplossingspartners.](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="next-steps"></a>Volgende stappen

Zie [Richtlijnen voor laadgegevens voor](data-loading-best-practices.md)het laden voor het laden van richtlijnen .
