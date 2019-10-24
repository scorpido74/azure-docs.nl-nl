---
title: Upgrade uw big data Analytics-oplossingen van Azure Data Lake Storage Gen1 naar Azure Data Lake Storage Gen2
description: Voer een upgrade uit voor uw oplossing voor het gebruik van Azure Data Lake Storage Gen2
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 02/07/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: rugopala
ms.openlocfilehash: 27d752b8ff7eafbb92930b19e17890ace8a90b85
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750436"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2"></a>Upgrade uw big data Analytics-oplossingen van Azure Data Lake Storage Gen1 naar Azure Data Lake Storage Gen2

Als u Azure Data Lake Storage Gen1 gebruikt in uw big data Analytics-oplossingen, helpt deze hand leiding u bij het upgraden van die oplossingen om Azure Data Lake Storage Gen2 te gebruiken. U kunt dit document gebruiken om de afhankelijkheden te beoordelen die uw oplossing op Data Lake Storage Gen1 heeft. In deze hand leiding wordt ook beschreven hoe u de upgrade kunt plannen en uitvoeren.

We helpen u bij het uitvoeren van de volgende taken:

: heavy_check_mark: uw upgrade gereedheids evalueren

: heavy_check_mark: plan voor een upgrade

: heavy_check_mark: de upgrade uitvoeren

## <a name="assess-your-upgrade-readiness"></a>Uw upgrade gereedheids evalueren

Ons doel is dat alle mogelijkheden die aanwezig zijn in Data Lake Storage Gen1, beschikbaar worden gesteld in Data Lake Storage Gen2. Hoe deze mogelijkheden worden weer gegeven, zoals in SDK, CLI enzovoort, kunnen verschillen tussen Data Lake Storage Gen1 en Data Lake Storage Gen2. Toepassingen en services die met Data Lake Storage Gen1 werken, moeten op dezelfde manier kunnen werken met Data Lake Storage Gen2. Ten slotte zijn sommige mogelijkheden niet meteen beschikbaar in Data Lake Storage Gen2. Zodra deze beschikbaar komen, kondigen we deze aan in dit document.

In deze volgende secties kunt u bepalen hoe het beste kan worden bijgewerkt naar Data Lake Storage Gen2 en wanneer het het meest zinvol is om dit mogelijk te maken.

### <a name="data-lake-storage-gen1-solution-components"></a>Data Lake Storage Gen1 oplossings onderdelen

Wanneer u Data Lake Storage Gen1 in uw analyse oplossingen of-pijp lijnen gebruikt, zijn er waarschijnlijk veel extra technologieën waarmee u de volledige end-to-end-functionaliteit kunt benutten. In dit [artikel](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) worden verschillende onderdelen van de gegevens stroom beschreven, waaronder opname, verwerking en verbruiks gegevens.

Daarnaast zijn er Kruis onderdelen waarmee u deze onderdelen kunt inrichten, beheren en bewaken. Elk van de onderdelen werkt met Data Lake Storage Gen1 met behulp van een interface die het meest geschikt is voor hen. Wanneer u van plan bent om uw oplossing bij te werken naar Data Lake Storage Gen2, moet u rekening houden met de interfaces die worden gebruikt. U moet de beheer interfaces en de gegevens interfaces upgraden, aangezien elke interface verschillende vereisten heeft.

![Data Lake Storage oplossings onderdelen](./media/data-lake-storage-upgrade/solution-components.png)

In **afbeelding 1** hierboven ziet u de onderdelen van de functie die u in de meeste analyse oplossingen zou zien.

In **afbeelding 2** ziet u een voor beeld van hoe deze onderdelen worden geïmplementeerd met behulp van specifieke technologieën.

De opslag functionaliteit in **afbeelding 1** wordt gegeven door data Lake Storage gen1 (**afbeelding 2**). U ziet hoe de verschillende onderdelen in de gegevens stroom communiceren met Data Lake Storage Gen1 met behulp van REST Api's of Java SDK. U ziet ook hoe de onderdelen van de functie voor cross-knippen communiceren met Data Lake Storage Gen1. Het inrichtings onderdeel maakt gebruik van Azure-resource sjablonen, terwijl het bewakings onderdeel dat Azure Monitor logboeken gebruikt, gebruikmaakt van operationele gegevens die afkomstig zijn van Data Lake Storage Gen1.

Als u een upgrade wilt uitvoeren van het gebruik van Data Lake Storage Gen1 naar Data Lake Storage Gen2, moet u de gegevens en meta gegevens kopiëren, de gegevens stromen opnieuw koppelen en vervolgens alle onderdelen moeten kunnen samen werken met Data Lake Storage Gen2.

De volgende secties bevatten informatie om u te helpen betere beslissingen te nemen:

: heavy_check_mark: platform mogelijkheden

: heavy_check_mark: programmeer interfaces

: heavy_check_mark: Azure ecosysteem

: heavy_check_mark: partner ecosysteem

: heavy_check_mark: operationele informatie

In elke sectie kunt u de "Most-is" bepalen voor uw upgrade. Nadat u zeker weet dat de mogelijkheden beschikbaar zijn, of als u zeker weet dat er redelijk tijdelijke oplossingen zijn, gaat u verder naar de sectie [een upgrade plannen](#planning-for-an-upgrade) van deze hand leiding.

### <a name="platform-capabilities"></a>Platformfunctionaliteiten

In deze sectie wordt beschreven welke mogelijkheden van Data Lake Storage Gen1 platform op dit moment beschikbaar zijn in Data Lake Storage Gen2.

| | Data Lake Storage Gen1 | Data Lake Storage Gen2-doel | Status van Beschik baarheid Data Lake Storage Gen2  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Gegevens organisatie| Biedt ondersteuning voor gegevens die zijn opgeslagen als mappen en bestanden | Biedt ondersteuning voor gegevens die zijn opgeslagen als objecten/blobs, evenals mappen en bestanden- [koppeling](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Biedt ondersteuning voor gegevens die zijn opgeslagen als mappen en bestanden die *nu beschikbaar zijn* <br><br> Ondersteunt gegevens die zijn opgeslagen als objecten/blobs- *nog niet beschikbaar* |
| Naamruimte| Gekoppelde | Gekoppelde |  *Nu beschikbaar*  |
| API  | REST API via HTTPS | REST API via HTTP/HTTPS| *Nu beschikbaar* |
| API aan server zijde| [WebHDFS-compatibele REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) | Azure Blob service REST API [Data Lake Storage Gen2 rest API](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Data Lake Storage Gen2 REST API – *nu beschikbaar* <br><br> Azure Blob service REST API- *nog niet beschikbaar*       |
| Hadoop File System-client | Ja ([Azure data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Ja ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Nu beschikbaar*  |  
| Gegevens bewerkingen: autorisatie  | POSIX-Access Control lijsten (Acl's) op het niveau van bestanden en mappen op basis van Azure Active Directory identiteiten  | Bestands-en mapniveau POSIX-Access Control lijsten (Acl's) op basis van de Azure Active Directory-Identities sleutel voor het [delen](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) van autorisatie rollen op account niveau, op basis van Access Control ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) voor toegang tot containers | *Nu beschikbaar* |
| Gegevens bewerkingen: Logboeken  | Ja | Eenmalige aanvragen voor logboeken voor specifieke duur met de integratie van Azure monitoring voor ondersteunings tickets | Eenmalige aanvragen voor logboeken voor specifieke duur met een ondersteunings ticket dat *nu beschikbaar is*<br><br> Integratie van Azure-bewaking – *nog niet beschikbaar* |
| Versleutelen van gegevens in rust | Transparant, aan de server zijde met door service beheerde sleutels en met door de klant beheerde sleutels in azure-sleutel kluis | Transparant, aan de server zijde met door service beheerde sleutels en met door de klant sleutels beheerde sleutels in azure-sleutel kluis | Door service beheerde sleutels: *nu beschikbaar*<br><br> Door de klant beheerde sleutels: *nu beschikbaar*  |
| Beheer bewerkingen (bijvoorbeeld account maken) | [Op rollen gebaseerd toegangs beheer](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) van Azure voor account beheer | [Op rollen gebaseerd toegangs beheer](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) van Azure voor account beheer | *Nu beschikbaar*|
| Sdk's van ontwikkel aars | .NET, Java, Python, node. js  | .NET, Java, Python, node. js, C++, Ruby, PHP, go, Android, Ios| *Nog niet beschikbaar* |
| |Geoptimaliseerde prestaties voor de werk belastingen van parallelle analyses. Hoge door Voer en IOPS. | Geoptimaliseerde prestaties voor de werk belastingen van parallelle analyses. Hoge door Voer en IOPS. | *Nu beschikbaar* |
| Ondersteuning voor Virtual Network (VNet)  | [Virtual Network-integratie gebruiken](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Service-eind punt gebruiken voor Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Nu beschikbaar* |
| Grootte limieten | Geen limieten voor de grootte van het account, de bestands grootte of het aantal bestanden | Geen limieten voor de grootte van het account of het aantal bestanden. De bestands grootte is beperkt tot 5 TB. | *Nu beschikbaar*|
| Geo-redundantie| Lokaal redundant (LRS) | Lokaal redundante (LRS) zone redundante (ZRS) Geo-redundante (GRS)-Geo-redundante Lees toegang (RA-GRS) Zie [hier](https://docs.microsoft.com/azure/storage/common/storage-redundancy) voor meer informatie| *Nu beschikbaar* |
| Regionale beschikbaarheid | [Hier](https://azure.microsoft.com/regions/) bekijken | Alle [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Nu beschikbaar*                                                                                                                           |
| Prijs                                       | [Prijzen](https://azure.microsoft.com/pricing/details/data-lake-store/) bekijken                                                                            | [Prijzen](https://azure.microsoft.com/pricing/details/storage/data-lake/) bekijken                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| SLA voor beschikbaarheid                            | [Zie SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Zie SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Nu beschikbaar*                                                                                                                           |
| Gegevensbeheer                             | Verlopen bestanden                                                                                                                                        | Levenscyclus beleid                                                                                                                                                                                                                                                                                                                                                                                                          | *Nog niet beschikbaar*                                                                                                                       |

### <a name="programming-interfaces"></a>Programmeerinterfaces

In deze tabel wordt beschreven welke API-sets beschikbaar zijn voor uw aangepaste toepassingen. Om iets duidelijker te maken, hebben we deze API-sets onderverdeeld in twee typen: beheer-Api's en File System-Api's.

Met beheer-Api's kunt u accounts beheren, terwijl bestandssysteem-Api's u helpen bij het uitvoeren van de bestanden en mappen.

|  API-set                           |  Data Lake Storage Gen1                                                                                                                                                                                                                                                                                                   | Beschik baarheid voor Data Lake Storage Gen2-met gedeelde sleutel verificatie | Beschik baarheid voor Data Lake Storage Gen2-met OAuth-verificatie                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .NET SDK-beheer                  | [Koppeling](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *Niet ondersteund*                                                      | *Nu beschikbaar-* [koppeling](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| .NET SDK – bestands systeem                  | [Koppeling](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | *Nog niet beschikbaar*                                                | *Nog niet beschikbaar*                                                                                                                                             |
| Java SDK-beheer                  | [Koppeling](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Niet ondersteund*                                                      | *Nu beschikbaar:* [koppeling](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| Java SDK – bestands systeem                  |   [koppelen](https://docs.microsoft.com/java/api/overview/azure/datalake)                                                                                                                                                                                                                                        | *Nog niet beschikbaar*                                                | *Nog niet beschikbaar*                                                                                                                                             |
| Node. js-beheer                   |   [koppelen](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                               | Niet ondersteund                                                      | *Nu beschikbaar-* [koppeling](https://azure.github.io/azure-storage-node/)                                                                                            |
| Node. js-bestands systeem                   |   [koppelen](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                               | *Nog niet beschikbaar*                                                | *Nog niet beschikbaar*                                                                                                                                             |
| Python-beheer                    |   [koppelen](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                | *Niet ondersteund*                                                      | *Nu beschikbaar-* [koppeling](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python-bestands systeem                    | [Koppeling](https://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | *Nog niet beschikbaar*                                                | *Nog niet beschikbaar*                                                                                                                                             |
| REST API-beheer                  | [Koppeling](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Niet ondersteund*                                                      | *Nu beschikbaar-*                                                                                                                                               |
| REST API-bestands systeem                  | [Koppeling](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | *Nu beschikbaar*                                                    | *Nu beschikbaar-* [koppeling](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| Power shell-beheer en bestands systeem | [Koppeling](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | Beheer-niet-ondersteund bestands systeem- *nog niet beschikbaar*        | Beheer – *nu beschikbaar-* [koppeling](https://docs.microsoft.com/powershell/module/az.storage) <br><br>Bestands systeem- *nog niet beschikbaar* |
| CLI: beheer                       | [Koppeling](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Niet ondersteund*                                                      | *Nu beschikbaar-* [koppeling](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| CLI-bestands systeem                       | [Koppeling](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | *Nog niet beschikbaar*                                                | *Nog niet beschikbaar*                                                                                                                                             |
| Azure Resource Manager sjablonen-beheer             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Niet ondersteund*                                                      | *Nu beschikbaar-* [koppeling](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Azure-ecosysteem

Wanneer u Data Lake Storage Gen1 gebruikt, kunt u een aantal micro soft-Services en-producten gebruiken in uw end-to-end-pijp lijnen. Deze services en producten werken direct of indirect met Data Lake Storage Gen1. In deze tabel ziet u een lijst met de services die zijn gewijzigd om te werken met Data Lake Storage Gen1 en geeft u op welke items momenteel compatibel zijn met Data Lake Storage Gen2.

| **Onderwerp**             | **Beschik baarheid voor Data Lake Storage Gen1**                                                                                                                                    | **Beschik baarheid voor Data Lake Storage Gen2-met gedeelde sleutel verificatie**                                                                                                           | **Beschik baarheid voor Data Lake Storage Gen2-met OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Analyse raamwerk  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Nu beschikbaar*                                                                                                                                                              | *Nu beschikbaar*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [Hdinsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3,6- *nu beschikbaar* Hdinsight 4,0- *nog niet beschikbaar*      | HDInsight 3,6 ESP: *nu beschikbaar* <br><br>  HDInsight 4,0 ESP- *nog niet beschikbaar*                                                                 |
|                      | Databricks Runtime 3,1 en hoger                                                                                                                                               | [Databricks Runtime 5,1 en hoger](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *-nu beschikbaar* | [Databricks Runtime 5,1 en hoger](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) – *nu beschikbaar*                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Niet ondersteund*                                                                                                                                                              | [Koppeling](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) *nu beschikbaar* |
| Data-integratie     | [Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [Versie 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) : *nu beschikbaar* versie 1: *niet ondersteund*                               | [Versie 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) : *nu beschikbaar* <br><br> Versie 1: *niet ondersteund*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Niet ondersteund*                                                                                                                                                              | *Niet ondersteund*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Nu beschikbaar*                                                                                                                                                          | *Nu beschikbaar*                                                                                                                             |
|                      | [Data Catalog](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Nog niet beschikbaar*                                                                                                                                                          | *Nog niet beschikbaar*                                                                                                                             |
|                      | [Logic Apps](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Nu beschikbaar*                                                                                                                                                          | *Nu beschikbaar*                                                                                                                             |
| IoT                  | [Event Hubs – vastleggen](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Nu beschikbaar*                                                                                                                                                          | *Nu beschikbaar*                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Nu beschikbaar*                                                                                                                                                          | *Nu beschikbaar*                                                                                                                             |
| Verbruik          | [Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Nu beschikbaar*                                                                                                                                                          | *Nu beschikbaar*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Nog niet beschikbaar*                                                                                                                                                          | *Nog niet beschikbaar*                                                                                                                             |
|                      | [Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Nog niet beschikbaar*                                                                                                                                                          | *Nog niet beschikbaar*                                                                                                                             |
| Productiviteit         | [Azure-portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Niet ondersteund*                                                                                                                                                              | Account beheer *– nu beschikbaar* <br><br>Gegevens bewerkingen *-*  *nog niet beschikbaar*                                                                   |
|                      | [Data Lake-Hulpprogram Ma's voor Visual Studio](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Nog niet beschikbaar*                                                                                                                                                          | *Nog niet beschikbaar*                                                                                                                             |
|                      | [Azure-opslagverkenner](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Nu beschikbaar*                                                                                                                                                              | *Nu beschikbaar*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Nog niet beschikbaar*                                                                                                                                                          | *Nog niet beschikbaar*                                                                                                                             |

### <a name="partner-ecosystem"></a>Partner ecosysteem

Deze tabel bevat een overzicht van de services en producten van derden die zijn aangepast om met Data Lake Storage Gen1 te werken. Ook wordt aangegeven welke onderdelen momenteel compatibel zijn met Data Lake Storage Gen2.

| Gebied            | Partner  | Product/service  | Beschik baarheid voor Data Lake Storage Gen1                                                                                                                                               | Beschik baarheid voor Data Lake Storage Gen2-met gedeelde sleutel verificatie                                                   | Beschik baarheid voor Data Lake Storage Gen2-met OAuth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Analyse raamwerk | Cloudera     | CDH                  | [Koppeling](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Nog niet beschikbaar*                                                                                                  | [Koppeling](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Koppeling](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Niet ondersteund*                                                                                                                  | *Nog niet beschikbaar*                                                                                                  |
|                     | HortonWorks  | HDP 3,0              |   [koppelen](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                      | *Nog niet beschikbaar*                                                                                                  | *Nog niet beschikbaar*                                                                                                  |
|                     | Qubole       |                      | [Koppeling](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Nog niet beschikbaar*                                                                                                  | *Nog niet beschikbaar*                                                                                                  |
| ETL                 | StreamSets   |                      | [Koppeling](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Nog niet beschikbaar*                                                                                                  | *Nog niet beschikbaar*                                                                                                  |
|                     | Informatica  |                      | [Koppeling](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Nog niet beschikbaar*                                                                                                  | *Nog niet beschikbaar*                                                                                                  |
|                     | Attunity     |                      | [Koppeling](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Nog niet beschikbaar*                                                                                                  | *Nog niet beschikbaar*                                                                                                  |
|                     | Alteryx      |                      | [Koppeling](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Nog niet beschikbaar*                                                                                                  | *Nog niet beschikbaar*                                                                                                  |
|                     | ImanisData   |                      | [Koppeling](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Nog niet beschikbaar*                                                                                                  | *Nog niet beschikbaar*                                                                                                  |
|                     | WANdisco     |                      | [Koppeling](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Koppeling](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Koppeling](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Operationele informatie

Data Lake Storage Gen1 pusht specifieke informatie en gegevens naar andere services die u helpen bij het operationeel maken van uw pijp lijnen. In deze tabel ziet u de beschik baarheid van overeenkomende ondersteuning in Data Lake Storage Gen2.

| Type gegevens                                                                                       | Beschik baarheid voor Data Lake Storage Gen1                                                                 | Beschik baarheid voor Data Lake Storage Gen2                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Facturerings gegevens: meters die worden verzonden naar een Commerce team voor facturering en vervolgens beschikbaar worden gesteld aan klanten  | *Nu beschikbaar*                                                                                             | *Nu beschikbaar*                                                                                                                           |
| Activiteitenlogboeken                                                                                          | [Koppeling](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Eenmalige aanvragen voor logboeken voor een specifieke duur met een ondersteunings ticket dat *nu beschikbaar is* Azure-bewakings integratie- *nog niet beschikbaar* |
| Diagnostische logboeken                                                                                        | [Koppeling](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | Eenmalige aanvragen voor logboeken voor een specifieke duur met een ondersteunings ticket dat *nu beschikbaar is* Azure-bewakings integratie- *nog niet beschikbaar* |
| Metrische gegevens                                                                                                | *Niet ondersteund*                                                                                               | *Nu beschikbaar-* [koppeling](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Een upgrade plannen

In deze sectie wordt ervan uitgegaan dat u de sectie [uw upgrade gereedheids](#assess-your-upgrade-readiness) van deze hand leiding hebt gecontroleerd en dat aan al uw afhankelijkheden is voldaan. Als er mogelijkheden zijn die nog niet beschikbaar zijn in Data Lake Storage Gen2, kunt u alleen door gaan als u de bijbehorende tijdelijke oplossingen kent. De volgende secties bevatten richt lijnen over hoe u een upgrade van uw pijp lijnen kunt plannen. Het uitvoeren van de daad werkelijke upgrade wordt beschreven in de sectie [een upgrade uitvoeren](#performing-the-upgrade) van deze hand leiding.

### <a name="upgrade-strategy"></a>Upgrade strategie

Het meest essentiële onderdeel van de upgrade is het bepalen van de strategie. Deze beslissing bepaalt welke keuzes voor u beschikbaar zijn.

Deze tabel bevat een aantal bekende strategieën die zijn gebruikt voor het migreren van data bases, Hadoop-clusters, enzovoort. We zullen vergelijk bare strategieën in onze richt lijnen aannemen en aanpassen aan onze context.

| Strategie                   | -Professionals                                                                                  | Nadelen                                                           | Wanneer gebruiken?                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Lift-and-Shift                 | Gemakkelijk.                                                                                 | Vereist uitval tijd voor het kopiëren van gegevens, het verplaatsen van taken en het verplaatsen van binnenkomend en uitgaand verkeer                                             | Voor een eenvoudigere oplossing, waarbij er niet meerdere oplossingen zijn die toegang hebben tot hetzelfde gen1-account en die daarom gezamenlijk kunnen worden verplaatst in een snel beheer.                                                  |
| Incrementeel kopiëren en kopiëren | Verminder uitval tijd door een kopie op de achtergrond uit te voeren terwijl het bron systeem nog steeds actief is. | Vereist uitval tijd voor het verplaatsen van inkomend en uitgaand verkeer.                   | De hoeveelheid gegevens die moet worden gekopieerd, is groot en de downtime voor levens duur en verschuiving is niet acceptabel. Tijdens de overgang kan het nodig zijn om te testen met aanzienlijke productie gegevens op het doel systeem. |
| Parallelle aanneming              | Met minste downtime kunt u tijd voor het migreren van toepassingen naar eigen goeddunken.           | Het is de meest voor deel, omdat twee richtings synchronisatie nodig is. | Voor complexe scenario's waarbij toepassingen die op Data Lake Storage Gen1 zijn gebouwd, niet allemaal tegelijk kunnen worden cutover en moet op een incrementele manier worden verplaatst.                                                      |

Hieronder vindt u meer informatie over de stappen die voor elk van de strategieën zijn betrokken. De stappen geven een lijst van wat u zou doen met de onderdelen die bij de upgrade horen. Dit omvat het algemene bron systeem, het algehele doel systeem, ingangs bronnen voor het bron systeem, uitvoer doelen voor het bron systeem en de taken die op het bron systeem worden uitgevoerd.

Deze stappen zijn niet bedoeld om te worden gescripteerd. Ze zijn bedoeld om het framework in te stellen over de manier waarop we op elke strategie denken. We bieden casestudy's voor elke strategie, aangezien ze worden geïmplementeerd.

#### <a name="lift-and-shift"></a>Lift-and-Shift

1. Het bron systeem onderbreken: binnenkomend bronnen, taken, uitgangs bestemmingen.
2. Kopieer alle gegevens van het bron systeem naar het doel systeem.
3. Wijs Alle ingangs bronnen naar het doel systeem. Ga naar het uitvoer doel van het doel systeem.
4. Verplaats, wijzig en voer alle taken uit op het doel systeem.
5. Het bron systeem uitschakelen.

#### <a name="copy-once-and-copy-incremental"></a>Kopiëren en eenmaal kopiëren

1. Kopieer de gegevens van het bron systeem naar het doel systeem.
2. Kopieer met regel matige tussen pozen de incrementele gegevens van het bron systeem naar het doel systeem.
3. Ga naar het uitvoer doel van het doel systeem.
4. Verplaats, wijzig en voer alle taken uit op het doel systeem.
5. Met punt worden bronnen stapsgewijs binnenkomen naar het doel systeem, conform uw gemak.
6. Zodra alle inkomende bronnen naar het doel systeem verwijzen.
    1. Incrementeel kopiëren uit te scha kelen.
    2. Het bron systeem uitschakelen.

#### <a name="parallel-adoption"></a>Parallelle aanneming

1. Stel het doel systeem in.
2. Stel een twee richtings replicatie in tussen het bron systeem en het doel systeem.
3. Met punt worden bronnen incrementeel binnenkomen naar het doel systeem.
4. Verplaats, wijzig en voer incrementeel taken uit naar het doel systeem.
5. Verwijs incrementeel naar uitgaande doelen vanuit het doel systeem.
6. Wanneer alle oorspronkelijke inkomende bronnen, taken en uitwijkings bestemming met het doel systeem werken, moet u het bron systeem uitschakelen.

### <a name="data-upgrade"></a>Gegevens upgrade

De algemene strategie die u gebruikt om uw upgrade uit te voeren (zoals beschreven in het gedeelte [upgrade strategie](#upgrade-strategy) van deze hand leiding), bepaalt de hulpprogram ma's die u kunt gebruiken voor de upgrade van uw gegevens. De hulpprogram ma's die hieronder worden weer gegeven, zijn gebaseerd op huidige informatie en zijn suggesties. 

#### <a name="tools-guidance"></a>Hulp bij hulpprogram ma's

| Strategie                       | Tools                                                                                                             | -Professionals                                                                                                                             | Overwegingen                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Lift-and-Shift**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Beheerde Cloud service                                                                                                                | Zowel gegevens als Acl's kunnen momenteel worden gekopieerd.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Bekende Hadoop-meegeleverde machtigingen voor het hulp programma, d.w.z. Acl's kunnen met dit hulp programma worden gekopieerd                                                   | Vereist een cluster waarmee gelijktijdig verbinding kan worden gemaakt met zowel Data Lake Storage Gen1 als Gen2.                                                                                                                                                                                   |
| **Incrementeel kopiëren en kopiëren** | Azure Data Factory                                                                                                    | Beheerde Cloud service                                                                                                                | Zowel gegevens als Acl's kunnen momenteel worden gekopieerd. Ter ondersteuning van incrementele kopieën in ADF, moeten gegevens in een tijd reeks worden georganiseerd. Het kortste interval tussen incrementele kopieën is [15 minuten](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). |
| **Parallelle aanneming**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Ondersteuning voor consistente replicatie als het gebruik van een pure Hadoop-omgeving die is verbonden met Azure Data Lake Storage, ondersteunt twee richtings replicatie | Als u geen zuivere Hadoop-omgeving gebruikt, kan er een vertraging optreden in de replicatie.                                                                                                                                                                                                                                                  |

Houd er rekening mee dat er derden zijn die de Data Lake Storage Gen1 op Data Lake Storage Gen2-upgrade kunnen afhandelen zonder dat hiervoor de bovenstaande hulpprogram ma's voor het kopiëren van gegevens en meta gegevens worden uitgevoerd (bijvoorbeeld: [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). Ze bieden een "one-stop shop"-ervaring voor het uitvoeren van gegevens migratie en de migratie van werk belastingen. Mogelijk moet u een out-of-band-upgrade uitvoeren voor alle hulpprogram ma's die zich buiten hun ecosysteem bevinden.

#### <a name="considerations"></a>Overwegingen

* U moet eerst het Data Lake Storage Gen2-account hand matig maken voordat u een deel van de upgrade begint, omdat de huidige instructies geen automatisch Gen2-account proces bevatten op basis van uw gen1-account gegevens. Zorg ervoor dat u de processen voor het maken van accounts voor [gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) en [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account)vergelijkt.

* Data Lake Storage Gen2 ondersteunt alleen bestanden met een grootte van Maxi maal 5 TB. Als u een upgrade naar Data Lake Storage Gen2 wilt uitvoeren, moet u mogelijk de grootte van de bestanden in Data Lake Storage Gen1 aanpassen zodat deze kleiner zijn dan 5 TB.

* Als u een hulp programma gebruikt dat geen Acl's kopieert of als u niet over de Acl's wilt kopiëren, moet u de Acl's op de bestemming hand matig op het juiste hoogste niveau instellen. U kunt dit doen met behulp van Storage Explorer. Zorg ervoor dat deze Acl's de standaard-Acl's zijn, zodat de bestanden en mappen die u kopieert over, worden overgenomen.

* In Data Lake Storage Gen1 is het hoogste niveau dat u Acl's kunt instellen de hoofdmap van het account. In Data Lake Storage Gen2 echter het hoogste niveau dat u Acl's kunt instellen bevindt zich in de hoofdmap van een container, niet op het hele account. Als u standaard-Acl's wilt instellen op account niveau, moet u deze op alle bestands systemen in uw Data Lake Storage Gen2-account dupliceren.

* Bestands naam beperkingen verschillen tussen de twee opslag systemen. Deze verschillen zijn met name van belang voor het kopiëren van Data Lake Storage Gen2 naar Data Lake Storage Gen1, omdat de laatste beperktere beperkingen hebben.

### <a name="application-upgrade"></a>Toepassingsupgrade

Wanneer u toepassingen wilt bouwen op Data Lake Storage Gen1 of Data Lake Storage Gen2, moet u eerst een geschikte programmeer interface kiezen. Wanneer u een API aanroept op die interface, moet u de juiste URI en de juiste referenties opgeven. De weer gave van deze drie elementen, de API, URI en hoe de referenties worden gegeven, verschilt van Data Lake Storage Gen1 en Data Lake Storage Gen2.So, als onderdeel van de upgrade van de toepassing moet u deze drie constructs op de juiste wijze toewijzen.

#### <a name="uri-changes"></a>URI-wijzigingen

De belangrijkste taak is hier om de URI te vertalen met een voor voegsel van `adl://` naar een URI met een `abfss://` voor voegsel.

Het URI-schema voor Data Lake Storage Gen1 wordt [in detail](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) beschreven, maar breed gering is het *adl://mydatalakestore.azuredatalakestore.net/\<file_path\>.*

Het URI-schema voor het openen van Data Lake Storage Gen2-bestanden wordt [hier](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) gedetailleerd beschreven, maar is in grote lijnen `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`.

U moet uw bestaande toepassingen door lopen en er zeker van zijn dat u de Uri's op de juiste manier hebt gewijzigd om naar Data Lake Storage Gen2 te verwijzen. U moet ook de juiste referenties toevoegen. Ten slotte moet u de oorspronkelijke toepassingen buiten gebruik stellen en vervangen door de nieuwe toepassing die nauw keurig moet worden afgestemd op uw algemene upgrade strategie.

#### <a name="custom-applications"></a>Aangepaste toepassingen

Afhankelijk van de interface die uw toepassing gebruikt met Data Lake Storage Gen1, moet u deze aanpassen om deze aan Data Lake Storage Gen2 aan te passen.

##### <a name="rest-apis"></a>REST API's

Als uw toepassing gebruikmaakt van Data Lake Storage REST Api's, moet u uw toepassing aanpassen voor gebruik van de Data Lake Storage Gen2 REST-Api's. Koppelingen worden gegeven in de sectie *programmeer interfaces* .

##### <a name="sdks"></a>SDK's

Zoals vermeld in de sectie *uw upgrade gereedheids evalueren* , zijn er momenteel geen sdk's beschikbaar. Als u wilt dat de poort van uw toepassingen wordt Data Lake Storage Gen2, wordt u aangeraden te wachten op ondersteunde Sdk's beschikbaar zijn.

##### <a name="powershell"></a>PowerShell

Zoals vermeld in de sectie uw upgrade gereedheids evalueren, is er momenteel geen Power Shell-ondersteuning beschikbaar voor het gegevens vlak.

U kunt de Commandlets van het beheer vlak vervangen door de juiste items in Data Lake Storage Gen2. Koppelingen worden gegeven in de sectie *programmeer interfaces* .

##### <a name="cli"></a>CLI

Zoals vermeld in *de sectie uw upgrade gereedheids evalueren* , is CLI-ondersteuning momenteel niet beschikbaar voor het gegevens vlak.

U kunt de opdrachten voor het beheer vlak vervangen door de betreffende items in Data Lake Storage Gen2. Koppelingen worden gegeven in de sectie *programmeer interfaces* .

### <a name="analytics-frameworks-upgrade"></a>Upgrade van Analytics Frameworks

Als uw toepassing meta gegevens maakt over informatie in de Store, zoals expliciete bestands-en mappaden, moet u extra acties uitvoeren na de upgrade van Store data/meta data. Dit is met name van toepassing op analyse raamwerken zoals Azure HDInsight, Databricks enzovoort, waarmee doorgaans catalogus gegevens in de Store-gegevens worden gemaakt.

Analyse raamwerken werken met gegevens en meta gegevens die zijn opgeslagen in de externe winkels, zoals Data Lake Storage Gen1 en Gen2. Daarom kunnen de motoren in theorie kortstondig zijn en alleen worden weer gegeven wanneer taken moeten worden uitgevoerd op basis van de opgeslagen gegevens.

Om de prestaties te optimaliseren, kunnen de analytische Frameworks echter expliciete verwijzingen naar de bestanden en mappen die zijn opgeslagen in de externe opslag maken en vervolgens een cache maken om deze te bewaren. Als de URI van de externe gegevens wijziging, bijvoorbeeld een cluster dat de gegevens in Data Lake Storage Gen1 eerder heeft opgeslagen en nu in Data Lake Storage Gen2 wilt opslaan, verschilt de URI voor dezelfde gekopieerde inhoud. Nadat de gegevens en meta gegevens zijn bijgewerkt, moeten de caches voor deze engines ook worden vernieuwd of opnieuw worden geïnitialiseerd.

Als onderdeel van het plannings proces moet u uw toepassing identificeren en nagaan hoe meta gegevens gegevens opnieuw kunnen worden geïnitialiseerd om te verwijzen naar gegevens die nu zijn opgeslagen in Data Lake Storage Gen2. Hieronder vindt u richt lijnen voor veelgestelde analytische frameworks om u te helpen bij het bijwerken van de stappen.

#### <a name="azure-databricks"></a>Azure Databricks

Afhankelijk van de upgrade strategie die u kiest, zijn de stappen verschillend. In de huidige sectie wordt ervan uitgegaan dat u de strategie voor levens en verschuiving hebt gekozen. De huidige Databricks-werk ruimte die is gebruikt voor toegang tot gegevens in een Data Lake Storage Gen1 account, wordt verwacht dat deze werkt met de gegevens die naar Data Lake Storage Gen2 account worden gekopieerd.

Zorg er eerst voor dat u het Gen2-account hebt gemaakt en vervolgens over gegevens en meta van gen1 naar Gen2 hebt gekopieerd met behulp van een geschikt hulp programma. Deze hulpprogram ma's worden in de sectie [gegevens upgrades](#data-upgrade) van deze hand leiding genoemd.

Vervolgens voert u een [upgrade uit](https://docs.azuredatabricks.net/user-guide/clusters/index.html) van uw bestaande Databricks-cluster om te beginnen met Databricks runtime 5,1 of hoger, die data Lake Storage Gen2 moet ondersteunen.

De stappen daarna worden uitgevoerd op basis van de manier waarop de bestaande Databricks-werk ruimte toegang heeft tot gegevens in het Data Lake Storage Gen1-account. U kunt dit doen door adl://-Uri's [rechtstreeks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) vanuit notebooks of via [mountpoints](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs)aan te roepen.

Als u rechtstreeks toegang hebt tot notebooks door de volledige adl://-Uri's op te geven, moet u elk notitie blok door lopen en de configuratie wijzigen om toegang te krijgen tot de bijbehorende Data Lake Storage Gen2-URI.

U moet deze opnieuw configureren om te verwijzen naar Data Lake Storage Gen2-account. Er zijn geen wijzigingen meer nodig en de notitie blokken moeten kunnen werken zoals voorheen.

Als u een van de andere upgrade strategieën gebruikt, kunt u een variant van de bovenstaande stappen maken om te voldoen aan uw vereisten.

### <a name="azure-ecosystem-upgrade"></a>Upgrade van Azure ecosysteem

Elk van de hulpprogram ma's en services die in de sectie [Azure ecosysteem](#azure-ecosystem) van deze hand leiding worden genoemd, moet worden geconfigureerd voor gebruik met data Lake Storage Gen2.

Zorg er eerst voor dat er integratie beschikbaar is met Data Lake Storage Gen2.

De elementen die hierboven worden genoemd (bijvoorbeeld: URI en referenties), moeten worden gewijzigd. U kunt de bestaande instantie die werkt met Data Lake Storage Gen1 wijzigen of u kunt een nieuw exemplaar maken dat met Data Lake Storage Gen2 zou werken.

### <a name="partner-ecosystem-upgrade"></a>Partner ecosysteem upgrade

Neem contact op met de partner waarmee u de onderdelen en hulpprogram ma's kunt gebruiken om ervoor te zorgen dat ze kunnen samen werken met Data Lake Storage Gen2. 

## <a name="performing-the-upgrade"></a>De upgrade uitvoeren

### <a name="pre-upgrade"></a>Vóór de upgrade

Als onderdeel hiervan zou u de sectie *uw upgrade voorbereiding evalueren* en de sectie [planning voor een upgrade](#planning-for-an-upgrade) van deze hand leiding door lopen, hebt u alle benodigde informatie ontvangen en hebt u een plan gemaakt dat aan uw behoeften voldoet. Tijdens deze fase wordt waarschijnlijk een test taak uitgevoerd.

### <a name="in-upgrade"></a>In-upgrade

Afhankelijk van de strategie die u kiest en de complexiteit van uw oplossing, kan deze fase een korte of een uitbrei ding zijn, waarbij meerdere werk belastingen wachten om incrementeel te worden verplaatst naar Data Lake Storage Gen2. Dit is het meest essentiële onderdeel van uw upgrade.

### <a name="post-upgrade"></a>Na de upgrade

Nadat u klaar bent met de overgangs bewerking, moet u de laatste stappen uitvoeren om een grondige verificatie uit te voeren. Dit is exclusief, maar niet beperkt tot het verifiëren van gegevens die op betrouw bare wijze zijn gekopieerd, Controleer of de Acl's correct zijn ingesteld, of end-to-end pijp lijnen goed werken, enzovoort. Nadat de verificaties zijn voltooid, kunt u uw oude pijp lijnen nu uitschakelen, uw bron Data Lake Storage Gen1 accounts verwijderen en de volledige snelheid van uw oplossingen op basis van Data Lake Storage Gen2.

## <a name="conclusion"></a>Conclusie

De richt lijnen in dit document moeten u hebben geholpen bij het upgraden van uw oplossing voor het gebruik van Data Lake Storage Gen2. 

Als u meer vragen hebt of feedback hebt, kunt u hieronder opmerkingen geven of feedback geven in het [Feedback forum van Azure](https://feedback.azure.com/forums/327234-data-lake).
