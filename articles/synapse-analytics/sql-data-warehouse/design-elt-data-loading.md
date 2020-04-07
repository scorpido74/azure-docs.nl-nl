---
title: In plaats van ETL, ontwerp ELT
description: Flexibele strategieën voor het laden van gegevens implementeren voor Synapse SQL-pool binnen Azure Synapse Analytics
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e99fd898956e11a4827d023691111a47e5a790c0
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744958"
---
# <a name="data-loading-strategies-for-synapse-sql-pool"></a>Strategieën voor het laden van gegevens voor Synapse SQL-pool

Traditionele SMP SQL-pools gebruiken een ETL-proces (Extract, Transform en Load) voor het laden van gegevens. Synapse SQL-pool, binnen Azure Synapse Analytics, heeft een enorm parallelle verwerkingsarchitectuur (MPP) die profiteert van de schaalbaarheid en flexibiliteit van reken- en opslagbronnen.

Met behulp van een EXTRACT, Load, and Transform (ELT) proces maakt gebruik van MPP en elimineert de middelen die nodig zijn voor gegevenstransformatie voorafgaand aan het laden.

Hoewel SQL-pool veel laadmethoden ondersteunt, waaronder populaire SQL Server-opties zoals [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) en de [SqlBulkCopy API,](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)is de snelste en meest schaalbare manier om gegevens te laden via externe tabellen van PolyBase en de [COPY-instructie](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (preview).

Met PolyBase en de instructie COPY hebt u toegang tot externe gegevens die zijn opgeslagen in Azure Blob-opslag of Azure Data Lake Store via de T-SQL-taal. Voor de meeste flexibiliteit bij het laden raden we u aan de copy-instructie te gebruiken.

> [!NOTE]  
> De copy-instructie bevindt zich momenteel in een openbare preview. Als u feedback wilt geven, stuurt sqldwcopypreview@service.microsoft.comu e-mail naar de volgende distributielijst: .

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="what-is-elt"></a>Wat is ELT?

Extract, Load, and Transform (ELT) is een proces waarbij gegevens worden geëxtraheerd uit een bronsysteem, in een SQL-groep worden geladen en vervolgens worden getransformeerd.

De basisstappen voor de implementatie van ELT zijn:

1. Extraheer de brongegevens naar tekstbestanden.
2. Land de gegevens in Azure Blob-opslag of Azure Data Lake Store.
3. Bereid de gegevens voor op het laden.
4. Laad de gegevens in faseringstabellen met PolyBase of de opdracht KOPIËREN.
5. Transformeer de gegevens.
6. Voeg de gegevens in productietabellen in.

Zie [PolyBase gebruiken om gegevens uit Azure blob-opslag te laden](load-data-from-azure-blob-storage-using-polybase.md)voor een zelfstudie voor het laden van PolyBase.

Zie [Patronen blog laden voor](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)meer informatie.

## <a name="1-extract-the-source-data-into-text-files"></a>1. De brongegevens extraheren in tekstbestanden

Het verkrijgen van gegevens uit uw bronsysteem is afhankelijk van de opslaglocatie.  Het doel is om de gegevens te verplaatsen naar PolyBase en de COPY ondersteunde afgebakende tekst of CSV-bestanden.

### <a name="polybase-and-copy-external-file-formats"></a>Externe bestandsindelingen (PolyBase en COPY)

Met PolyBase en de instructie COPY u gegevens laden van UTF-8- en UTF-16-gecodeerde gedefinieerde tekst- of CSV-bestanden. Naast de afgebakende tekst- of CSV-bestanden, worden deze ook geladen vanuit de Hadoop-bestandsindelingen zoals ORC en Parket. PolyBase en de COPY-instructie kunnen ook gegevens van Gzip- en Snappy-gecomprimeerde bestanden laden.

Uitgebreide ASCII-indeling, indeling met vaste breedte en geneste indelingen zoals WinZip of XML worden niet ondersteund. Als u exporteert vanuit SQL Server, u het [bcp-opdrachtregelgereedschap](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) gebruiken om de gegevens naar afgebakende tekstbestanden te exporteren.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. De gegevens in Azure Blob-opslag of Azure Data Lake Store verwerken

Als u de gegevens wilt verwerken in Azure-opslag, u deze verplaatsen naar [Azure Blob-opslag](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) of [Azure Data Lake Store Gen2.](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Op beide locaties moeten de gegevens worden opgeslagen in tekstbestanden. PolyBase en de COPY-instructie kunnen vanaf beide locaties worden geladen.

Hulpprogramma's en services die u gebruiken om gegevens naar Azure Storage te verplaatsen:

- [Azure ExpressRoute-service](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) verbetert de netwerkdoorvoer, -prestaties en -voorspelbaarheid. ExpressRoute is een service die uw gegevens via een speciale privéverbinding naar Azure leidt. ExpressRoute-verbindingen leiden gegevens niet via het openbare internet. De verbindingen bieden meer betrouwbaarheid, hogere snelheden, lagere latencies en hogere beveiliging dan typische verbindingen via het openbare internet.
- [AZCopy-hulpprogramma](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) verplaatst gegevens naar Azure Storage via het openbare internet. Dit werkt als uw gegevensgrootte minder dan 10 TB beslaat. Om regelmatig ladingen uit te voeren met AZCopy, test je de netwerksnelheid om te zien of deze acceptabel is.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) heeft een gateway die u op uw lokale server installeren. Vervolgens u een pijplijn maken om gegevens van uw lokale server naar Azure Storage te verplaatsen. Zie [Gegevens laden voor SQL Analytics](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)voor het gebruik van Data Factory met SQL Analytics.

## <a name="3-prepare-the-data-for-loading"></a>3. De gegevens voorbereiden op het laden

Mogelijk moet u de gegevens in uw opslagaccount voorbereiden en schoonmaken voordat u ze laadt. Gegevensvoorbereiding kan worden uitgevoerd terwijl uw gegevens in de bron zijn, terwijl u de gegevens exporteert naar tekstbestanden of nadat de gegevens zich in Azure Storage bevinden.  Het is het gemakkelijkst om zo vroeg mogelijk met de gegevens te werken.  

### <a name="define-external-tables"></a>Externe tabellen definiëren

Als u PolyBase gebruikt, moet u externe tabellen in uw SQL-groep definiëren voordat u deze laadt. Externe tabellen zijn niet vereist door de kopie-instructie. PolyBase gebruikt externe tabellen om de gegevens in Azure Storage te definiëren en te openen.

Een externe tabel is vergelijkbaar met een databaseweergave. De externe tabel bevat het tabelschema en wijst naar gegevens die buiten de SQL-groep zijn opgeslagen.

Het definiëren van externe tabellen omvat het opgeven van de gegevensbron, de indeling van de tekstbestanden en de tabeldefinities. T-SQL-syntaxisverwijzingsartikelen die u nodig hebt, zijn:

- [EXTERNE GEGEVENSBRON MAKEN](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [EXTERNE TABEL MAKEN](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Bij het laden van parket is de SQL-toewijzing van het gegevenstype:

| **Gegevenstype parket** | **SQL-gegevenstype** |
| :-------------------: | :---------------: |
|        tinyint        |      tinyint      |
|       smallint        |     smallint      |
|          int          |        int        |
|        bigint         |      bigint       |
|        booleaans        |        bit        |
|        double         |       float       |
|         float         |       real        |
|        double         |       Geld       |
|        double         |    kleingeld     |
|        tekenreeks         |       Nchar       |
|        tekenreeks         |     nvarchar      |
|        tekenreeks         |       Char        |
|        tekenreeks         |      varchar      |
|        binair         |      binair       |
|        binair         |     varbinary varbinary     |
|       tijdstempel       |       date        |
|       tijdstempel       |   smalldatetijd   |
|       tijdstempel       |     datetime2     |
|       tijdstempel       |     datum/tijd      |
|       tijdstempel       |       tijd        |
|         date          |       date        |
|        decimal        |      decimal      |

Zie de stap Externe tabellen [maken](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) in de bewerkvoorbereidingshandleiding voor een voorbeeld van het maken van externe objecten.

### <a name="format-text-files"></a>Tekstbestanden opmaken

Als u PolyBase gebruikt, moeten de gedefinieerde externe objecten de rijen van de tekstbestanden uitlijnen met de externe tabel en de definitie van bestandsindeling. De gegevens in elke rij van het tekstbestand moeten worden uitgelijnd met de tabeldefinitie.
Ga als u de tekstbestanden opmaak:

- Als uw gegevens afkomstig zijn van een niet-relationele bron, moet u deze omzetten in rijen en kolommen. Of de gegevens nu afkomstig zijn van een relationele of niet-relationele bron, de gegevens moeten worden getransformeerd om af te stemmen op de kolomdefinities voor de tabel waarin u van plan bent de gegevens te laden.
- Gegevens in het tekstbestand opmaken om uit te lijnen met de kolommen en gegevenstypen in de doeltabel. Verkeerde uitlijning tussen gegevenstypen in de externe tekstbestanden en de SQL-pooltabel zorgt ervoor dat rijen tijdens de belasting worden afgewezen.
- Afzonderlijke velden in het tekstbestand met een terminator.  Zorg ervoor dat u een teken of een tekenreeks gebruikt die niet in uw brongegevens wordt gevonden. Gebruik de terminator die u hebt opgegeven met [EXTERNE BESTANDSINDELING MAKEN](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. De gegevens laden met PolyBase of de instructie COPY

Het is de beste gewoonte om gegevens in een faseringstabel te laden. Met faseringstabellen u fouten afhandelen zonder de productietabellen te verstoren. Een faseringstabel biedt u ook de mogelijkheid om de SQL-pool MPP te gebruiken voor gegevenstransformaties voordat u de gegevens in productietabellen invoegt.

De tabel moet vooraf worden gemaakt bij het laden in een faseringstabel met COPY.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>Opties voor laden met polybase- en COPY-instructie

Als u gegevens wilt laden met PolyBase, u een van de volgende laadopties gebruiken:

- [PolyBase met T-SQL](load-data-from-azure-blob-storage-using-polybase.md) werkt goed wanneer uw gegevens zich in Azure Blob-opslag of Azure Data Lake Store bevinden. Het geeft u de meeste controle over het laadproces, maar vereist ook dat u externe gegevensobjecten definieert. De andere methoden definiëren deze objecten achter de schermen terwijl u brontabellen naar doeltabellen mapt.  Als u T-SQL-belastingen wilt orkestreren, u Azure Data Factory-, SSIS- of Azure-functies gebruiken.
- [PolyBase met SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) werkt goed wanneer uw brongegevens zich in SQL Server bevinden, sql server on-premises of in de cloud. SSIS definieert de bron voor toewijzingen van doeltabeltoewijzingen en orkestreert ook de belasting. Als u al SSIS-pakketten hebt, u de pakketten aanpassen om te werken met de nieuwe bestemming voor gegevensmagazijnen.
- [PolyBase- en COPY-instructie met Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) is een ander orchestration-hulpmiddel.  Het definieert een pijplijn en plant taken.
- [PolyBase met Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) brengt gegevens van een tabel over naar een Databricks-gegevensframe en/of schrijft gegevens van een Databricks-gegevensframe naar een tabel met PolyBase.

### <a name="other-loading-options"></a>Andere laadopties

Naast PolyBase en de COPY-instructie u [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) of de [SqlBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)gebruiken. bcp wordt rechtstreeks naar de database geladen zonder azure blob-opslag te doorlopen en is alleen bedoeld voor kleine belastingen.

> [!NOTE]
> De laadprestaties van deze opties zijn trager dan PolyBase en de instructie COPY.

## <a name="5-transform-the-data"></a>5. De gegevens transformeren

Terwijl gegevens zich in de faseringstabel bevinden, voert u transformaties uit die uw werkbelasting vereist. Verplaats de gegevens vervolgens naar een productietabel.

## <a name="6-insert-the-data-into-production-tables"></a>6. De gegevens in productietabellen invoegen

De INSERT IN ... Select-instructie verplaatst de gegevens van de faseringstabel naar de permanente tabel.

Terwijl u een ETL-proces ontwerpt, probeert u het proces uit te voeren op een klein testvoorbeeld. Probeer 1000 rijen uit de tabel naar een bestand te halen, verplaats het naar Azure en probeer het vervolgens in een faseringstabel te laden.

## <a name="partner-loading-solutions"></a>Oplossingen voor het laden van partners

Veel van onze partners hebben laadoplossingen. Zie voor meer informatie een lijst van onze [oplossingspartners.](sql-data-warehouse-partner-business-intelligence.md)

## <a name="next-steps"></a>Volgende stappen

Zie voor hulp bij het laden [Richtlijnen voor het laden van gegevens](guidance-for-loading-data.md).
