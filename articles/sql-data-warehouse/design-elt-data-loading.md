---
title: Ontwerp ELT in plaats van ETL
description: In plaats van ETL kunt u een uitpak-, laad-en transformatie proces (ELT) ontwerpen voor het laden van gegevens of Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load-data
ms.date: 11/07/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 9220bf0cf94eaae6ddc945e83deac2a6041158d2
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748519"
---
# <a name="data-loading-strategies-for-azure-sql-data-warehouse"></a>Strategieën voor het laden van gegevens voor Azure SQL Data Warehouse

Traditionele SMP-data warehouses maken gebruik van een proces voor het laden van de gegevens extract, Transform en load (ETL). Azure SQL Data Warehouse is een enorm parallelle verwerkings architectuur (MPP) die gebruikmaakt van de schaal baarheid en flexibiliteit van reken-en opslag resources. Het gebruik van een extractie-, laad-en Transform-proces (ELT) kan profiteren van MPP en resources elimineren die nodig zijn om de gegevens te transformeren voordat ze worden geladen. Hoewel SQL Data Warehouse veel methoden voor het laden ondersteunt, waaronder populaire SQL Server opties zoals BCP en de SQL BulkCopy-API, is de snelste en meest schaal bare manier om gegevens te laden via Poly base externe tabellen en de [instructie Copy](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (preview).  Met poly base en de instructie COPY kunt u toegang krijgen tot externe gegevens die zijn opgeslagen in Azure Blob Storage of Azure Data Lake Store via de T-SQL-taal. Voor de meeste flexibiliteit bij het laden in SQL Data Warehouse, raden we u aan de instructie COPY te gebruiken. 

> [!NOTE]  
> De instructie COPY is momenteel beschikbaar als open bare preview. Als u feedback wilt geven, verzendt u een e-mail naar de volgende distributie lijst: sqldwcopypreview@service.microsoft.com.
>
        
 
> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]


## <a name="what-is-elt"></a>Wat is ELT?

Extra heren, laden en transformeren (ELT) is een proces waarmee gegevens worden geëxtraheerd uit een bron systeem, in een Data Warehouse worden geladen en vervolgens worden getransformeerd. 

De basis stappen voor het implementeren van ELT voor SQL Data Warehouse zijn:

1. Extraheer de bron gegevens in tekst bestanden.
2. Voer de gegevens in op Azure Blob-opslag of Azure Data Lake Store.
3. De gegevens voorbereiden voor het laden van.
4. Laad de gegevens in SQL Data Warehouse faserings tabellen met poly base of de Kopieer opdracht. 
5. Transformeer de gegevens.
6. Voeg de gegevens in productie tabellen in.


Zie poly [Base gebruiken voor het laden van gegevens uit Azure Blob-opslag naar Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)voor een zelf studie over het laden van poly bases.

Zie [blog voor het laden van patronen](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)voor meer informatie. 


## <a name="1-extract-the-source-data-into-text-files"></a>1. Extraheer de bron gegevens in tekst bestanden

Het ophalen van gegevens uit uw bron systeem is afhankelijk van de opslag locatie.  Het doel is om de gegevens naar poly Base te verplaatsen en de kopie ondersteunt tekst of CSV-bestanden met scheidings tekens. 

### <a name="polybase-and-copy-external-file-formats"></a>Poly base en kopiëren van externe bestands indelingen

Met poly base en de instructie COPY kunt u gegevens laden van UTF-8-en UTF-16-gecodeerde tekst of CSV-bestanden met scheidings tekens. Behalve tekst-of CSV-bestanden, worden ze geladen vanuit de Hadoop-bestands indelingen, zoals ORC en Parquet. Poly base en de instructie COPY kunnen ook gegevens laden uit gzip-en Snappy gecomprimeerde bestanden. Uitgebreide ASCII-indeling met een vaste breedte en geneste indelingen, zoals WinZip of XML, worden niet ondersteund. Als u exporteert vanaf SQL Server, kunt u het [opdracht regel programma BCP](/sql/tools/bcp-utility?view=azure-sqldw-latest) gebruiken om de gegevens te exporteren naar tekst bestanden met scheidings tekens. 

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Voer de gegevens over naar Azure Blob-opslag of Azure Data Lake Store

Als u de gegevens in azure Storage wilt laten staan, kunt u deze verplaatsen naar [Azure Blob-opslag](../storage/blobs/storage-blobs-introduction.md) of [Azure data Lake Store Gen2](../data-lake-store/data-lake-store-overview.md). In beide locaties moeten de gegevens worden opgeslagen in tekst bestanden. Poly base en de instructie COPY kunnen worden geladen vanuit een van beide locaties.

Hulpprogram ma's en services die u kunt gebruiken om gegevens naar Azure Storage te verplaatsen:

- De [Azure ExpressRoute](../expressroute/expressroute-introduction.md) -service verbetert de netwerk doorvoer, prestaties en voorspel baarheid. ExpressRoute is een service waarmee uw gegevens worden gerouteerd via een speciale particuliere verbinding met Azure. ExpressRoute-verbindingen sturen geen gegevens via het open bare Internet. De verbindingen bieden meer betrouw baarheid, hogere snelheden, lagere wacht tijden en hogere beveiliging dan typische verbindingen via het open bare Internet.
- Met het [hulp programma AZCopy](../storage/common/storage-moving-data.md) kunt u gegevens verplaatsen naar Azure Storage via het open bare Internet. Dit werkt als uw gegevens grootten minder dan 10 TB zijn. Als u de belasting regel matig wilt uitvoeren met AZCopy, test u de netwerk snelheid om te controleren of deze acceptabel is. 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) heeft een gateway die u op uw lokale server kunt installeren. Vervolgens kunt u een pijp lijn maken om gegevens van uw lokale server naar Azure Storage te verplaatsen. Zie [gegevens laden in SQL Data Warehouse](/azure/data-factory/load-azure-sql-data-warehouse)als u Data Factory wilt gebruiken met SQL Data Warehouse.


## <a name="3-prepare-the-data-for-loading"></a>3. de gegevens voorbereiden voor het laden

Mogelijk moet u de gegevens in uw opslag account voorbereiden en opschonen voordat u deze in SQL Data Warehouse kunt laden. Gegevens voorbereiding kan worden uitgevoerd terwijl de gegevens zich in de bron bevinden, terwijl u de gegevens naar tekst bestanden exporteert of wanneer de gegevens zich in Azure Storage bevinden.  Het is eenvoudig om zo snel mogelijk in het proces te werken met de gegevens.  

### <a name="define-external-tables"></a>Externe tabellen definiëren

Als u poly base gebruikt, moet u externe tabellen definiëren in uw data warehouse voordat u ze laadt. Externe tabellen zijn niet vereist voor de instructie COPY. Poly base gebruikt externe tabellen voor het definiëren en openen van de gegevens in Azure Storage. Een externe tabel is vergelijkbaar met een database weergave. De externe tabel bevat het tabel schema en verwijst naar gegevens die buiten het Data Warehouse zijn opgeslagen. 

Als u externe tabellen definieert, moet u de gegevens bron, de indeling van de tekst bestanden en de tabel definities opgeven. T-SQL-syntaxis onderwerpen die u nodig hebt, zijn:
- [EXTERNE GEGEVENS BRON MAKEN](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest)
- [EXTERNE TABEL MAKEN](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest)

Bij het laden van Parquet is het gegevens type toewijzing met SQL DW:

| **Parquet-gegevens type** |                      **SQL-gegevens type**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        booleaans        |                             bitmask                              |
|        double         |                            float                             |
|         float         |                             realistische                             |
|        double         |                            financieel                             |
|        double         |                          smallmoney                          |
|        tekenreeks         |                            nchar                             |
|        tekenreeks         |                           nvarchar                           |
|        tekenreeks         |                             char                             |
|        tekenreeks         |                           varchar                            |
|        waarde         |                            waarde                            |
|        waarde         |                          varbinary                           |
|       tijdstempel       |                             date                             |
|       tijdstempel       |                        smalldatetime                         |
|       tijdstempel       |                          DATETIME2                           |
|       tijdstempel       |                           datum/tijd                           |
|       tijdstempel       |                             tijd                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

Voor een voor beeld van het maken van externe objecten raadpleegt u de stap [externe tabellen maken](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) in de zelf studie voor het laden van.

### <a name="format-text-files"></a>Tekst bestanden opmaken

Als u poly base gebruikt, moeten de gedefinieerde externe objecten de rijen van de tekst bestanden uitlijnen met de definitie van de externe tabel en de bestands indeling. De gegevens in elke rij van het tekst bestand moeten worden uitgelijnd met de tabel definitie.
De tekst bestanden opmaken:

- Als uw gegevens afkomstig zijn uit een niet-relationele bron, moet u deze omzetten in rijen en kolommen. Of de gegevens afkomstig zijn uit een relationele of niet-relationele bron, dat de gegevens moeten worden getransformeerd om te worden uitgelijnd met de kolom definities voor de tabel waarin u de gegevens wilt laden. 
- Gegevens in het tekst bestand opmaken om uit te lijnen met de kolommen en gegevens typen in de SQL Data Warehouse doel tabel. Een onjuiste uitlijning tussen gegevens typen in de externe tekst bestanden en de Data Warehouse tabel zorgt ervoor dat rijen worden afgewezen tijdens het laden.
- Scheid velden in het tekst bestand met een terminator.  Zorg ervoor dat u een teken of een teken reeks gebruikt die niet in uw bron gegevens is gevonden. Gebruik de Terminator die u hebt opgegeven met [externe BESTANDS indeling maken](/sql/t-sql/statements/create-external-file-format-transact-sql).


## <a name="4-load-the-data-into-sql-data-warehouse-staging-tables-using-polybase-or-the-copy-statement"></a>4. gegevens in SQL Data Warehouse faserings tabellen laden met poly base of de instructie COPY

Het is best practice om gegevens in een faserings tabel te laden. Met faserings tabellen kunt u fouten afhandelen zonder de productie tabellen te verstoren. Een faserings tabel biedt u ook de mogelijkheid om SQL Data Warehouse MPP te gebruiken voor gegevens transformaties voordat u de gegevens in productie tabellen invoegt. De tabel moet vooraf worden gemaakt wanneer deze in een faserings tabel met een kopie wordt geladen.

### <a name="options-for-loading-with-polybase-and-copy-statement"></a>Opties voor het laden met de instructie poly base en kopiëren

Als u gegevens wilt laden met poly Base, kunt u een van de volgende laad opties gebruiken:

- [Poly Base met T-SQL](load-data-from-azure-blob-storage-using-polybase.md) werkt goed als uw gegevens zich in Azure Blob storage of Azure data Lake Store bevinden. Het biedt u de meeste controle over het laad proces, maar u moet ook externe gegevens objecten definiëren. Met de andere methoden worden deze objecten achter de schermen gedefinieerd wanneer u bron tabellen aan doel tabellen toewijst.  U kunt met behulp van Azure Data Factory-, SSIS-of Azure-functies voor het organiseren van T-SQL-belasting. 
- [Poly Base met SSIS](/sql/integration-services/load-data-to-sql-data-warehouse) werkt goed als uw bron gegevens zich in SQL Server bevinden, hetzij SQL Server on-premises of in de Cloud. SSIS definieert de bron-naar-doel tabel toewijzingen en organiseert ook de belasting. Als u al SSIS-pakketten hebt, kunt u de pakketten wijzigen om met de nieuwe Data Warehouse-bestemming te werken. 
- [Poly base-en copy-instructie met Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) is een andere Orchestration-tool.  Hierin worden een pijp lijn en plannings taken gedefinieerd. 
- [Poly Base met Azure Databricks](../azure-databricks/databricks-extract-load-sql-data-warehouse.md) gegevens overdraagt van een SQL Data Warehouse tabel naar een Databricks data frame en/of schrijft gegevens van een Databricks data frame naar een SQL Data Warehouse-tabel met poly base.

### <a name="other-loading-options"></a>Andere opties voor laden

Naast poly base en de instructie COPY kunt u gebruikmaken van [bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest) of de SQLBULKCOPY- [API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). BCP wordt rechtstreeks naar SQL Data Warehouse geladen zonder Azure Blob-opslag te passeren en is uitsluitend bedoeld voor kleine belastingen. Houd er rekening mee dat de belasting prestaties van deze opties lager zijn dan poly base en de instructie COPY. 


## <a name="5-transform-the-data"></a>5. de gegevens transformeren

Terwijl de gegevens zich in de faserings tabel bevindt, voert u trans formaties uit die nodig zijn voor uw werk belasting. Verplaats de gegevens vervolgens naar een productie tabel.


## <a name="6-insert-the-data-into-production-tables"></a>6. Voeg de gegevens in productie tabellen in

INVOEGEN in... Met de instructie SELECT worden de gegevens van de faserings tabel naar de permanente tabel verplaatst. 

Probeer tijdens het ontwerpen van een ETL-proces het proces uit te voeren op een kleine steek proef. Probeer 1000 rijen uit de tabel naar een bestand te halen, verplaats het naar Azure en probeer het te laden in een faserings tabel. 


## <a name="partner-loading-solutions"></a>Oplossingen voor het laden van partners

Veel van onze partners hebben het laden van oplossingen. Zie een lijst met onze [oplossings partners](sql-data-warehouse-partner-business-intelligence.md)voor meer informatie. 


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [richt lijnen voor het laden van gegevens](guidance-for-loading-data.md)voor meer informatie.


