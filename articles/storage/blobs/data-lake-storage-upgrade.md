---
title: Upgrade Azure Data Lake Storage van gen1 naar Gen2
description: Voer een upgrade uit voor Azure Data Lake Storage van gen1 naar Gen2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 11/19/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: rugopala
ms.openlocfilehash: 9302cb8c78766611518139437abd666394c6206c
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894229"
---
# <a name="upgrade-azure-data-lake-storage-from-gen1-to-gen2"></a>Upgrade Azure Data Lake Storage van gen1 naar Gen2

Als u Azure Data Lake Storage Gen1 gebruikt in uw big data Analytics-oplossingen, helpt deze hand leiding u bij het upgraden van die oplossingen om Azure Data Lake Storage Gen2 te gebruiken. U kunt dit document gebruiken om de afhankelijkheden die uw oplossing op Data Lake Storage Gen1 heeft vast te stellen. Deze handleiding leest u ook hoe om te plannen en uitvoeren van de upgrade.

Wij helpen u via de volgende taken:

: heavy_check_mark: beoordelen van de gereedheid voor upgrade

: heavy_check_mark: plannen voor een upgrade

: heavy_check_mark: de upgrade uitvoert

## <a name="assess-your-upgrade-readiness"></a>Beoordeling van de gereedheid voor upgrade

Ons doel is dat de mogelijkheden die aanwezig in Data Lake Storage Gen1 zijn wordt beschikbaar gesteld in Data Lake Storage Gen2. Hoe deze mogelijkheden bijvoorbeeld worden weergegeven in de SDK, CLI enz., verschillen tussen Data Lake Storage Gen1 en Gen2 van Data Lake-opslag. Toepassingen en services die met Data Lake Storage Gen1 werken moeten op dezelfde manier werken met Data Lake Storage Gen2. Ten slotte zijn enkele van de mogelijkheden niet beschikbaar in Data Lake Storage Gen2 meteen. Zodra deze beschikbaar komen, maken we ze in dit document.

Deze volgende secties kunt u bepalen hoe het beste om te upgraden naar Data Lake Storage Gen2, en wanneer het kan zinvol zijn de meeste om dit te doen.

### <a name="data-lake-storage-gen1-solution-components"></a>Data Lake Storage Gen1 oplossingsonderdelen

Zeer waarschijnlijk wanneer u Data Lake Storage Gen1 in uw analytics-oplossingen of pijplijnen, zijn er veel extra technologieën die u gebruiken voor het bereiken van de algehele end-to-end-functionaliteit. Dit [artikel](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) beschrijft verschillende onderdelen van de gegevensstroom, zoals opnemen, verwerken en gebruiken van gegevens.

Er zijn bovendien algemene onderdelen kunt inrichten, beheren en bewaken van deze onderdelen. Elk van de onderdelen werken met Data Lake Storage Gen1 met behulp van een best passen bij deze interface. Wanneer u van plan bent om te upgraden van uw oplossing naar Data Lake Storage Gen2, moet u rekening houden met de interfaces die worden gebruikt. U moet zowel de beheerinterfaces als gegevensinterfaces upgraden omdat elke interface verschillende vereisten heeft.

![Data Lake Storage Solution Components](./media/data-lake-storage-upgrade/solution-components.png)

**Afbeelding 1** ziet u boven de functionaliteit van onderdelen op de dat u in de meeste analytics-oplossingen ziet.

**Afbeelding 2** toont een voorbeeld van hoe deze onderdelen zal worden geïmplementeerd met behulp van specifieke technologieën.

De functionaliteit voor opslaan in **afbeelding 1** wordt geleverd door Data Lake Storage Gen1 (**afbeelding 2**). Houd er rekening mee hoe de verschillende onderdelen in de gegevensstroom interactief werken met Data Lake Storage Gen1 met behulp van REST-API's of Java-SDK. Houd er ook rekening mee hoe de onderdelen van de functionaliteit transversale werken met Data Lake Storage Gen1. Het inrichtings onderdeel maakt gebruik van Azure-resource sjablonen, terwijl het bewakings onderdeel dat Azure Monitor logboeken gebruikt, gebruikmaakt van operationele gegevens die afkomstig zijn van Data Lake Storage Gen1.

Als u een oplossing uit met behulp van Data Lake Storage Gen1 naar Data Lake Storage Gen2 bijwerken, moet u de gegevens en metagegevens kopiëren, de gegevensstromen opnieuw koppelen en vervolgens alle onderdelen moeten kunnen werken met Data Lake Storage Gen2.

De volgende secties bevatten informatie over u betere beslissingen kunt komen:

: heavy_check_mark: platformmogelijkheden

: heavy_check_mark: programmeerinterfaces

: heavy_check_mark: Azure-ecosysteem

: heavy_check_mark: partnerecosysteem

: heavy_check_mark: operationele gegevens

In elke sectie zult u kunnen bepalen de 'musts' voor de upgrade. Nadat u zeker van zijn er dat de mogelijkheden die beschikbaar zijn, of u zeker van zijn er dat er redelijke oplossingen beschikbaar in plaats zijn, gaat u verder met de [plannen voor een upgrade](#planning-for-an-upgrade) sectie van deze handleiding.

### <a name="platform-capabilities"></a>Platformfunctionaliteiten

Deze sectie wordt beschreven welke mogelijkheden voor het platform van Data Lake Storage Gen1 die momenteel beschikbaar in Data Lake Storage Gen2 zijn.

| | Data Lake Storage Gen1 | Data Lake Storage Gen2 - doel | Data Lake Storage Gen2 - status van beschikbaarheid  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Gegevens kunnen worden geordend| Biedt ondersteuning voor gegevens die zijn opgeslagen als bestanden en mappen | Biedt ondersteuning voor gegevens die zijn opgeslagen als objecten/blobs en mappen en bestanden - [koppeling](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Biedt ondersteuning voor gegevens die zijn opgeslagen als mappen en bestanden die *nu beschikbaar zijn* <br><br> Ondersteunt gegevens die zijn opgeslagen als objecten/blobs- *nog niet beschikbaar* |
| Naamruimte| Hiërarchische | Hiërarchische |  *Nu beschikbaar*  |
| API  | REST-API via HTTPS | REST-API via HTTP/HTTPS| *Nu beschikbaar* |
| Server-side-API| [WebHDFS compatibele REST-API](https://msdn.microsoft.com/library/azure/mt693424.aspx) | REST-API van Azure Blob-Service [Gen2 REST API van Data Lake-opslag](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Data Lake Storage Gen2 REST API – *nu beschikbaar* <br><br> Azure Blob service REST API – *nu beschikbaar*       |
| Hadoop-bestand System-Client | Ja ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Ja ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Nu beschikbaar*  |  
| Gegevensbewerkingen – autorisatie  | Bestanden en mappen op niveau POSIX toegangsbeheerlijsten (ACL's) op basis van Azure Active Directory-identiteiten  | Bestanden en mappen op niveau POSIX toegangsbeheerlijsten (ACL's) op basis van Azure Active Directory-identiteiten [sleutel voor delen](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) voor account niveau autorisatie rollen gebaseerd toegangsbeheer ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) voor toegang tot containers | *Nu beschikbaar* |
| Gegevensbewerkingen – Logboeken  | Ja | Ja | *Nu beschikbaar* (preview-versie). Zie [bekende problemen](data-lake-storage-known-issues.md).<br><br> Integratie van Azure-bewaking – *nog niet beschikbaar* |
| Van Versleutelingsgegevens in rust | Serverzijde transparant, met de service beheerde sleutels en door de klant beheerde sleutels in Azure Key Vault | Transparant en bestanden op de Server met de service beheerde sleutels en sleutels van de klant beheerde sleutels in Azure Key Vault | Door service beheerde sleutels: *nu beschikbaar*<br><br> Door de klant beheerde sleutels: *nu beschikbaar*  |
| Bewerkingen (bijvoorbeeld-Account maken) | [Op rollen gebaseerd toegangsbeheer](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) verstrekt door Azure voor accountbeheer | [Op rollen gebaseerd toegangsbeheer](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) verstrekt door Azure voor accountbeheer | *Nu beschikbaar*|
| SDK's voor ontwikkelaars | .NET, Java, Python, Node.js  | .NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS| BLOB SDK: *nu beschikbaar*. [Azure data Lake Storage GEN2 SDK](https://azure.microsoft.com/blog/extended-filesystem-programming-capabilities-in-azure-data-lake-storage/)- *nu beschikbaar-.net, Java, python (open bare preview)*  |
| |Geoptimaliseerde prestaties voor werkbelastingen voor parallelle analyse. Hoge doorvoer en IOPS. | Geoptimaliseerde prestaties voor werkbelastingen voor parallelle analyse. Hoge doorvoer en IOPS. | *Nu beschikbaar* |
| Ondersteuning voor Virtual Network (VNet)  | [Virtual Network-integratie gebruiken](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Service-eind punt gebruiken voor Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Nu beschikbaar* |
| Maximale grootte | Geen limieten voor grootte van accounts, bestanden of het aantal bestanden | Er zijn geen limieten voor grootte van accounts of aantal bestanden. De bestandsgrootte is beperkt tot 5TB. | *Nu beschikbaar*|
| Geo-redundantie| Lokaal redundant (LRS) | Lokaal redundante (LRS) zone redundante (ZRS) Geo-redundante (GRS)-Geo-redundante Lees toegang (RA-GRS) Zie [hier](https://docs.microsoft.com/azure/storage/common/storage-redundancy) voor meer informatie| *Nu beschikbaar* |
| Regionale beschikbaarheid | Zie [hier](https://azure.microsoft.com/regions/) | Alle [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Nu beschikbaar*                                                                                                                           |
| Prijs                                       | Zie [prijzen](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | Zie [prijzen](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| SLA voor beschikbaarheid                            | [Zie de SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Zie de SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Nu beschikbaar*                                                                                                                           |
| Gegevensbeheer                             | Verloopdatum van bestand                                                                                                                                        | Lifecycle-beleid                                                                                                                                                                                                                                                                                                                                                                                                          | Levenscyclus beleid *nu beschikbaar* (preview-versie). Zie [bekende problemen](data-lake-storage-known-issues.md).                                                                                                                     |

### <a name="programming-interfaces"></a>Programmeerinterfaces

Deze tabel wordt beschreven welke API ingesteld die beschikbaar voor uw aangepaste toepassingen zijn. SDK-ondersteuning voor ACL en bewerkingen op Directory-niveau worden nog niet ondersteund.

|  API-set                           |  Data Lake Storage Gen1                                                                                                                                                                                                                                                                                                   | Beschikbaarheid voor Data Lake Storage Gen2 - met verificatie op basis gedeelde sleutel | Beschikbaarheid voor Data Lake Storage Gen2 - met OAuth-verificatie                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .NET SDK                  | [Koppeling](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet) | *Nu beschikbaar* | *Nu beschikbaar* |
| Java SDK                | [Koppeling](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Nu beschikbaar*                                                      | *Nu beschikbaar*                                     |
| Node.js                | [Koppeling](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | *Nu beschikbaar*                                                     | *Nu beschikbaar*                                                                                           |
| Python-SDK                   | [Koppeling](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *Nu beschikbaar*                                                      | *Nu beschikbaar*                                        |
| REST-API                 | [Koppeling](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Nu beschikbaar*                                                      | *Nu beschikbaar*                                                                                                                                               |
| PowerShell | [Koppeling](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | *Nu beschikbaar* |
| CLI                 | [Koppeling](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Nu beschikbaar*                                                      | *Nu beschikbaar*                                                               |
| Azure Resource Manager-sjablonen - management             | [Sjabloon1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Nu beschikbaar*                                                      | *Nu beschikbaar*                                          |

### <a name="azure-ecosystem"></a>Azure-ecosysteem

Wanneer u Data Lake Storage Gen1 gebruikt, kunt u diverse Microsoft-services en producten in uw end-to-end-pijplijnen. Deze producten en services werken met Data Lake Storage Gen1 direct of indirect. Deze tabel bevat een overzicht van de services die we hebt gewijzigd om te werken met Data Lake Storage Gen1 en ziet u welke momenteel compatibel zijn met Data Lake Storage Gen2.

| **Onderwerp**             | **De beschikbaarheid van Data Lake Storage Gen1**                                                                                                                                    | **Beschikbaarheid voor Data Lake Storage Gen2 – met verificatie op basis gedeelde sleutel**                                                                                                           | **De beschikbaarheid van Data Lake Storage Gen2 – met OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Analytics-framework  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Nu beschikbaar*                                                                                                                                                              | *Nu beschikbaar*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [HDInsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3.6 - *nu beschikbaar* HDInsight 4.0 - *nog niet beschikbaar*      | HDInsight 3,6 ESP: *nu beschikbaar* <br><br>  HDInsight 4,0 ESP- *nog niet beschikbaar*                                                                 |
|                      | Databricks Runtime 3.1 en hoger                                                                                                                                               | [Databricks Runtime 5,1 en hoger](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *-nu beschikbaar* | [Databricks Runtime 5,1 en hoger](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) – *nu beschikbaar*                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Niet ondersteund*                                                                                                                                                              | [Koppeling](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) *nu beschikbaar* |
| Data-integratie     | [Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [Versie 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) : *nu beschikbaar* versie 1: *niet ondersteund*                               | [Versie 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) : *nu beschikbaar* <br><br> Versie 1: *niet ondersteund*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Niet ondersteund*                                                                                                                                                              | *Niet ondersteund*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Nu beschikbaar*                                                                                                                                                          | *Nu beschikbaar*                                                                                                                             |
|                      | [Data Catalog](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Nog niet beschikbaar*                                                                                                                                                          | *Nog niet beschikbaar*                                                                                                                             |
|                      | [Logic Apps](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Nu beschikbaar*                                                                                                                                                          | *Nu beschikbaar*                                                                                                                             |
| IoT                  | [Eventhubs-vastleggen](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Nu beschikbaar*                                                                                                                                                          | *Nu beschikbaar*                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Nu beschikbaar*                                                                                                                                                          | *Nu beschikbaar*                                                                                                                             |
| Verbruik          | [Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Nu beschikbaar*                                                                                                                                                          | *Nu beschikbaar*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Nog niet beschikbaar*                                                                                                                                                          | *Nog niet beschikbaar*                                                                                                                             |
|                      | [Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Nog niet beschikbaar*                                                                                                                                                          | *Nog niet beschikbaar*                                                                                                                             |
| Productiviteit         | [Azure-portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Niet ondersteund*                                                                                                                                                              | Account beheer *– nu beschikbaar* <br><br>Gegevens bewerkingen *-*  *nog niet beschikbaar*                                                                   |
|                      | [Data Lake Tools voor Visual Studio](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Nog niet beschikbaar*                                                                                                                                                          | *Nog niet beschikbaar*                                                                                                                             |
|                      | [Azure-opslagverkenner](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Nu beschikbaar*                                                                                                                                                              | *Nu beschikbaar*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Nog niet beschikbaar*                                                                                                                                                          | *Nog niet beschikbaar*                                                                                                                             |

### <a name="partner-ecosystem"></a>Partnerecosysteem

Deze tabel bevat een overzicht van de services van derden en producten die zijn aangepast om te werken met Data Lake Storage Gen1. Ook ziet u welke momenteel compatibel zijn met Data Lake Storage Gen2.

| Gebied            | Partner  | Product/Service  | De beschikbaarheid van Data Lake Storage Gen1                                                                                                                                               | Beschikbaarheid voor Data Lake Storage Gen2 – met verificatie op basis gedeelde sleutel                                                   | De beschikbaarheid van Data Lake Storage Gen2 – met Oauth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Analytics-framework | Cloudera     | CDH                  | [Koppeling](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Nog niet beschikbaar*                                                                                                  | [Koppeling](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Koppeling](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Niet ondersteund*                                                                                                                  | *Nog niet beschikbaar*                                                                                                  |
|                     | HortonWorks  | HDP 3.0              | [Koppeling](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | *Nog niet beschikbaar*                                                                                                  | *Nog niet beschikbaar*                                                                                                  |
|                     | Qubole       |                      | [Koppeling](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | [Koppeling](https://docs.qubole.com/en/latest/quick-start-guide/Azure-quick-start-guide/QuboleConnect/detailed-azure-qubole-steps/azure-Azure-setup.html#step-4c-configure-azure-data-lake-gen-2-storage-with-keys)                                                                                                  | [Koppeling](https://docs.qubole.com/en/latest/quick-start-guide/Azure-quick-start-guide/QuboleConnect/detailed-azure-qubole-steps/azure-Azure-setup.html#step-4d-configure-azure-data-lake-gen-2-storage-with-tokens)                                                                                                 |
| ETL                 | StreamSets   |                      | [Koppeling](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Nog niet beschikbaar*                                                                                                  | *Nog niet beschikbaar*                                                                                                  |
|                     | Informatica  |                      | [Koppeling](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Nog niet beschikbaar*                                                                                                  | *Nog niet beschikbaar*                                                                                                  |
|                     | Attunity     |                      | [Koppeling](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Nog niet beschikbaar*                                                                                                  | *Nog niet beschikbaar*                                                                                                  |
|                     | Alteryx      |                      | [Koppeling](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Nog niet beschikbaar*                                                                                                  | *Nog niet beschikbaar*                                                                                                  |
|                     | ImanisData   |                      | [Koppeling](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Nog niet beschikbaar*                                                                                                  | *Nog niet beschikbaar*                                                                                                  |
|                     | WANdisco     |                      | [Koppeling](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Koppeling](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Koppeling](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Operationele gegevens

Data Lake Storage Gen1 pushes specifieke informatie en gegevens naar andere services die helpt u om uw pijplijnen operationeel te maken. Deze tabel toont de beschikbaarheid van de bijbehorende ondersteuning in Data Lake Storage Gen2.

| Type gegevens                                                                                       | De beschikbaarheid van Data Lake Storage Gen1                                                                 | De beschikbaarheid van Data Lake Storage Gen2                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Factureringsgegevens - meters die worden verzonden naar commerce-team voor facturering en vervolgens beschikbaar gesteld aan klanten  | *Nu beschikbaar*                                                                                             | *Nu beschikbaar*                                                                                                                           |
| Activiteitenlogboeken                                                                                          | [Koppeling](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Speciale aanvragen voor logboeken voor het gebruik van bepaalde duur ondersteuningsticket – *nu beschikbaar* integratie van Azure Monitoring - *nog niet beschikbaar* |
| Diagnostische logboeken                                                                                        | [Koppeling](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | *Nu beschikbaar* (preview-versie). Zie [bekende problemen](data-lake-storage-known-issues.md). <br>Integratie van Azure-bewaking- *nog niet beschikbaar* |
| Metrische gegevens                                                                                                | *Niet ondersteund*                                                                                               | *Er zijn nu beschikbaar -* [koppeling](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Planning voor een upgrade

Deze sectie wordt ervan uitgegaan dat u hebt bekeken de [beoordelen van de gereedheid voor upgrade](#assess-your-upgrade-readiness) sectie van deze handleiding, en dat alle afhankelijkheden wordt voldaan. Als er mogelijkheden die nog steeds niet beschikbaar in Data Lake Storage Gen2 zijn, gaat u alleen als u weet dat de bijbehorende oplossingen. De volgende secties vindt u richtlijnen over hoe u voor de upgrade van uw pijplijnen plannen kunt. Uitvoeren van de daadwerkelijke upgrade worden beschreven in de [uitvoeren van de upgrade](#performing-the-upgrade) sectie van deze handleiding.

### <a name="upgrade-strategy"></a>Strategie voor een upgrade uitvoert

De meest kritieke onderdeel van de upgrade is de strategie bepalen. Deze beslissing bepaalt de beschikbare opties.

Deze tabel bevat een aantal bekende strategieën die zijn gebruikt voor het migreren van data bases, Hadoop-clusters, enzovoort. We zullen vergelijk bare strategieën in onze richt lijnen aannemen en aanpassen aan onze context.

| Strategie                   | Professionals                                                                                  | Nadelen                                                           | Wanneer gebruikt u?                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Lift-and-shift                 | Eenvoudigste.                                                                                 | Voor het kopiëren van gegevens, het verplaatsen van taken, en verplaatsen inkomend en uitgaand verkeer te worden stilgelegd                                             | Voor eenvoudiger oplossingen, wanneer er niet meerdere oplossingen toegang tot de dezelfde Gen1-account en kan daarom samen kunnen worden verplaatst in een snel beheerde manier.                                                  |
| Kopiëren-once-en-kopiëren incrementele | Verminder downtime door te kopiëren op de achtergrond uitvoeren terwijl bronsysteem nog steeds actief is. | Worden stilgelegd voor het verplaatsen van inkomend en uitgaand verkeer.                   | Hoeveelheid gegevens moeten worden gekopieerd via is groot en de downtime die zijn gekoppeld aan leven-and-shift wordt niet geaccepteerd. Testen is mogelijk vereist met aanzienlijke productiegegevens op het doelsysteem voor overgang. |
| Parallelle ingebruikname              | Hiermee kunt u instellen-tijd van de minimale downtime voor toepassingen om te migreren naar eigen goeddunken.           | Meest uitgebreide sinds 2 richtingen synchronisatie is vereist tussen de twee systemen. | Voor complexe scenario's waarin toepassingen die zijn gebouwd op Data Lake Storage Gen1 cutover in één keer kunnen niet worden en moeten worden verplaatst via alleen de gewijzigde.                                                      |

Hieronder vindt u meer informatie over de stappen die betrokken zijn voor elk van de strategieën. De stappen lijst met wat u zou doen met de onderdelen die betrokken zijn bij de upgrade. Dit omvat het algehele bronsysteem, algemene doelsysteem, inkomend bronnen voor het bronsysteem, uitgaande bestemmingen voor bronsysteem en taken die worden uitgevoerd op bronsysteem.

Deze stappen zijn niet bedoeld om te worden voorgeschreven. Ze zijn bedoeld om in te stellen van het framework over hoe we over elke strategie nadenken. We bieden casestudy's voor elke strategie zoals we zien dat deze wordt geïmplementeerd.

#### <a name="lift-and-shift"></a>Lift-and-shift

1. Het bronsysteem – inkomend bronnen, taken, uitgaande bestemmingen onderbreken.
2. Kopieer alle gegevens uit het bronsysteem naar het doelsysteem.
3. Alle inkomende bronnen, wijs in het doelsysteem. Verwijzen naar de bestemming uitgaand verkeer van het doelsysteem.
4. Verplaatsen, wijzigen, voert u alle taken op het doelsysteem.
5. Het bronsysteem uitschakelen.

#### <a name="copy-once-and-copy-incremental"></a>Kopiëren-zodra en incrementele kopie

1. Kopieer de gegevens uit het bronsysteem naar het doelsysteem.
2. Kopieer de incrementele gegevens uit het bronsysteem naar het doelsysteem met regelmatige intervallen.
3. Verwijzen naar de bestemming uitgaand verkeer van het doelsysteem.
4. Verplaatsen, wijzigen, voert u alle taken op het doelsysteem.
5. Inkomend verkeer bronnen incrementeel verwijzen naar het doelsysteem aan de hand van uw gemak.
6. Zodra alle inkomende bronnen die naar het doelsysteem verwijst zijn.
    1. Uitschakelen incrementeel kopiëren.
    2. Het bronsysteem uitschakelen.

#### <a name="parallel-adoption"></a>Parallelle ingebruikname

1. Instellen van het doelsysteem.
2. Instellen van een replicatie in twee richtingen tussen de bron en doel.
3. Inkomend verkeer bronnen incrementeel verwijzen naar het doelsysteem.
4. Verplaatsen, wijzigen, voert u taken incrementeel aan het doelsysteem.
5. Wijs incrementeel aan uitgaande bestemmingen van het doelsysteem.
6. Nadat alle oorspronkelijke inkomend bronnen, taken en bestemming voor uitgaand verkeer met het doelsysteem werkt, schakelt u uit het bronsysteem.

### <a name="data-upgrade"></a>De upgrade van gegevens

De algehele strategie die u kunt de upgrade uitvoert (die worden beschreven in de [Upgrade strategie](#upgrade-strategy) sectie van deze handleiding), bepaalt de hulpprogramma's die u voor de upgrade van uw gegevens gebruiken kunt. De onderstaande hulpprogramma's zijn gebaseerd op actuele informatie en suggesties. 

#### <a name="tools-guidance"></a>Richtlijnen voor hulpprogramma 's

| Strategie                       | Tools                                                                                                             | Professionals                                                                                                                             | Overwegingen                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Lift-and-shift**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Beheerde cloudservice                                                                                                                | Zowel gegevens als Acl's kunnen momenteel worden gekopieerd.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Bekende hulpprogramma voor Hadoop-opgegeven machtigingen, dat wil zeggen ACL's kan met dit hulpprogramma worden gekopieerd                                                   | Een cluster die verbinding met zowel Data Lake Storage Gen1 en Gen2 op hetzelfde moment maken kan is vereist.                                                                                                                                                                                   |
| **Kopiëren-once-en-kopiëren incrementele** | Azure Data Factory                                                                                                    | Beheerde cloudservice                                                                                                                | Zowel gegevens als Acl's kunnen momenteel worden gekopieerd. Gegevens moeten worden ingedeeld in een time series-manier ter ondersteuning van incrementeel kopiëren in ADF. Het kortste interval tussen incrementele kopieën is [15 minuten](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). |
| **Parallelle ingebruikname**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Ondersteuning voor consistente replicatiegroep als met behulp van een zuivere Hadoop-omgeving is verbonden met Azure Data Lake Storage biedt ondersteuning voor replicatie in twee richtingen | Als u niet een zuivere Hadoop-omgeving, kan dit worden veroorzaakt door een vertraging in de replicatie.                                                                                                                                                                                                                                                  |

Houd er rekening mee dat er zich van derden die de Data Lake Storage Gen1 naar Data Lake Storage Gen2 upgrade zonder de tussenkomst van de bovenstaande gegevens/meta-gegevens kopiëren van hulpprogramma's kunnen worden verwerkt (bijvoorbeeld: [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). Ze bieden een 'one-stop shop'-ervaring waarmee u de migratie van gegevens, evenals de migratie van werkbelastingen. Mogelijk moet u een out-of-band-upgrade uitvoeren voor alle hulpprogramma's die zich buiten het ecosysteem.

#### <a name="considerations"></a>Overwegingen

* U moet handmatig maken het Data Lake Storage Gen2 account eerst voordat u begint met een deel van de upgrade, omdat de huidige richtlijnen bevat geen een automatische Gen2 account proces op basis van de gegevens van uw Gen1-account. Zorg ervoor dat u de processen voor het maken van accounts voor het vergelijken [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) en [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account).

* Data Lake Storage Gen2 alleen ondersteunt bestanden tot 5 TB in grootte. Als u een upgrade naar Data Lake Storage Gen2 wilt uitvoeren, moet u mogelijk de grootte van de bestanden in Data Lake Storage Gen1 aanpassen zodat deze kleiner zijn dan 5 TB.

* Als u een hulpprogramma dat ACL's niet kopiëren of u niet wilt kopiëren via de ACL's, klikt u vervolgens moet u om in te stellen de ACL's op de doelcomputer handmatig op het juiste hoogste niveau. U kunt dat doen met behulp van Storage Explorer. Zorg ervoor dat de ACL's de standaard-ACL's zodanig in dat de bestanden en mappen die u via kopiëren overnemen.

* In Data Lake Storage Gen1 is het hoogste niveau, kunt u instellen dat ACL's in de hoofdmap van het account. In Data Lake Storage Gen2 echter het hoogste niveau dat u Acl's kunt instellen bevindt zich in de hoofdmap van een container, niet op het hele account. Dus als u wilt de standaard-ACL's op accountniveau ingesteld, u dubbele servers in het bestandssysteem in uw Data Lake Storage Gen2-account moet.

* Bestand naamsbeperkingen verschillen tussen de twee opslagsystemen. Deze verschillen zijn met name wanneer betreffende kopiëren van Data Lake Storage Gen2 naar Data Lake Storage Gen1 sinds de laatste meer beperkingen beperkte heeft.

### <a name="application-upgrade"></a>Toepassingsupgrade

Wanneer u nodig hebt om toepassingen te bouwen op Data Lake Storage Gen1 of Data Lake Storage Gen2, hebt u eerst een juiste programmeerinterface kiezen. Bij het aanroepen van een API voor die interface hebt u de juiste URI en de juiste referenties opgeven. De weergave van deze drie elementen, de API, URI, en hoe de referenties zijn opgegeven, zijn verschillend voor Data Lake Storage Gen1 en Data Lake Storage Gen2.So als onderdeel van de upgrade van de toepassing, moet u deze drie constructies op de juiste wijze worden toegewezen.

#### <a name="uri-changes"></a>URI-wijzigingen

De belangrijkste taak is hier om de URI te vertalen met een voor voegsel van `adl://` naar een URI met een `abfss://` voor voegsel.

Het URI-schema voor Data Lake Storage Gen1 wordt vermeld [hier](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) in detail, maar breed spreken, is het *adl://mydatalakestore.azuredatalakestore.net/\<bestandspad\>.*

Het URI-schema voor het openen van Data Lake Storage Gen2-bestanden wordt [hier](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) gedetailleerd beschreven, maar is in grote lijnen `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`.

U moet doorlopen van uw bestaande toepassingen en zorg ervoor dat u hebt gewijzigd de URI's op de juiste manier om te verwijzen naar Data Lake Storage Gen2 opvallend. U moet ook de juiste referenties toevoegen. Ten slotte moeten hoe u de oorspronkelijke toepassingen buiten gebruik stellen en vervangen door de nieuwe toepassing nauw worden afgestemd op uw strategie voor algehele upgrade.

#### <a name="custom-applications"></a>Aangepaste toepassingen

Afhankelijk van de interface voor die uw toepassing met Data Lake Storage Gen1 gebruikt, moet u deze wijzigen om aan te passen aan de Data Lake Storage Gen2.

##### <a name="rest-apis"></a>REST API's

Als uw toepassing gebruikmaakt van Data Lake Storage REST API's, moet u uw toepassing voor het gebruik van de Data Lake Storage Gen2 REST-API wijzigen. Vindt u koppelingen *programmeerinterfaces* sectie.

##### <a name="sdks"></a>SDK's

Als met de naam in de *beoordelen van de gereedheid voor upgrade* sectie, SDK's niet op dit moment beschikbaar zijn. Als u wilt dat de poort van uw toepassingen wordt Data Lake Storage Gen2, wordt u aangeraden te wachten op ondersteunde Sdk's beschikbaar zijn.

##### <a name="powershell"></a>PowerShell

Zoals beschreven in de evalueren de gereedheid voor upgrade-sectie, is PowerShell-ondersteuning momenteel niet beschikbaar voor de gegevenslaag.

U kunt management vlak commandlets vervangen met de juiste namen in Data Lake Storage Gen2. Vindt u koppelingen *programmeerinterfaces* sectie.

##### <a name="cli"></a>CLI

Als met de naam in *beoordelen van de gereedheid voor upgrade* sectie CLI-ondersteuning is momenteel niet beschikbaar voor de gegevenslaag.

U kunt management vlak opdrachten vervangen met de juiste namen in Data Lake Storage Gen2. Vindt u koppelingen *programmeerinterfaces* sectie.

### <a name="analytics-frameworks-upgrade"></a>Upgrade Analytics frameworks

Als uw toepassing metagegevens over de informatie in de store, zoals expliciete bestands- en mappaden maakt, moet u de volgende bewerkingen uitvoeren nadat gegevens/meta-gegevens in de upgrade hebt uitgevoerd. Dit geldt met name van analytics frameworks zoals Azure HDInsight, enz. Databricks, die meestal gegevens in de catalogus te op de gegevens op te slaan maken.

Analytics-frameworks werken met gegevens en metagegevens die zijn opgeslagen in de externe stores, zoals Data Lake Storage Gen1 en Gen2. Dus in theorie, kunnen de engines tijdelijke, en worden opgeroepen alleen wanneer taken moeten worden uitgevoerd voor de opgeslagen gegevens.

Echter voor het optimaliseren van prestaties, de analytics-frameworks mogelijk expliciete verwijzingen naar de bestanden en mappen die zijn opgeslagen in het archief met externe maken, en maak vervolgens een cache voor het opslaan van deze. De URI van de externe gegevens verandert, bijvoorbeeld een cluster die opslaan van gegevens in Data Lake Storage Gen1 eerder en nu willen opslaan in Data Lake Storage Gen2 is zijn de URI voor dezelfde gekopieerde inhoud verschillend. Dus nadat de gegevens en metagegevens een upgrade uitgevoerd voor de cache voor deze engines moet ook worden bijgewerkt of opnieuw geïnitialiseerd

Als onderdeel van het planningsproces moet u uw toepassing te identificeren en te achterhalen hoe meta-gegevens kan worden opnieuw geïnitialiseerd om te verwijzen naar gegevens die nu in Data Lake Storage Gen2 opgeslagen worden. Hieronder vindt u richtlijnen voor het algemeen aanvaarde analytics frameworks voor hulp bij de stappen voor de upgrade.

#### <a name="azure-databricks"></a>Azure Databricks

Afhankelijk van de upgrade strategie die u kiest, worden de stappen verschillen. De huidige sectie wordt ervan uitgegaan dat u ervoor de 'Leven-and-shift'-strategie gekozen hebt. Bovendien wordt de bestaande Databricks-werkruimte die u gebruikt voor toegang tot gegevens in een Data Lake Storage Gen1 account werken met de gegevens die wordt gekopieerd naar Data Lake Storage Gen2 account verwacht.

Controleer eerst of u hebt het Gen2-account hebt gemaakt en vervolgens gekopieerd gegevens en metagegevens van Gen1 naar Gen2 met behulp van een geschikt hulpprogramma. Deze hulpprogramma's worden specifiek genoemd [voor bijwerken van gegevens](#data-upgrade) sectie van deze handleiding.

Vervolgens [upgrade](https://docs.azuredatabricks.net/user-guide/clusters/index.html) uw bestaande Databricks-cluster aan de slag met Databricks runtime 5.1 of hoger, waarop ondersteuning voor Data Lake Storage Gen2 bieden moet.

De stappen zijn daarna gebaseerd op hoe de bestaande Databricks-werkruimte heeft toegang gegevens in het Data Lake Storage Gen1-account tot. Kan worden gedaan door het aanroepende adl: / / URI's [rechtstreeks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) van notitieblokken, of via [quorumbron:](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs).

Als u rechtstreeks vanuit notitieblokken openen wilt door op te geven van de volledige adl: / / URI's, moet u elk notitieblok doorlopen en de configuratie voor toegang tot de overeenkomstige Data Lake Storage Gen2 URI wijzigen.

Voortaan kunt moet u deze om te verwijzen naar Data Lake Storage Gen2 account opnieuw te configureren. Geen wijzigingen meer nodig zijn, en de notebooks kan werken als voorheen.

Als u een van de upgrade strategieën gebruikt, kunt u een variant van de bovenstaande stappen om te voldoen aan uw vereisten.

### <a name="azure-ecosystem-upgrade"></a>Upgrade van de Azure-ecosysteem

Elk van de hulpprogramma's en services die worden beschreven in [Azure-ecosysteem](#azure-ecosystem) sectie van deze handleiding moet worden geconfigureerd om te werken met Data Lake Storage Gen2.

Eerst, zorg ervoor dat er integratie met Data Lake Storage Gen2 beschikbaar.

Vervolgens wordt de elementen genoemd boven (bijvoorbeeld: URI en referenties), moet worden gewijzigd. Kunt u het bestaande exemplaar dat met Data Lake Storage Gen1 werkt wijzigen of u een nieuw exemplaar die met Data Lake Storage Gen2 werken kan maken.

### <a name="partner-ecosystem-upgrade"></a>Upgrade van de partner-ecosysteem

Neem contact op met de partner die het onderdeel en de hulpprogramma's om te controleren of dat ze kunnen werken met Data Lake Storage Gen2. 

## <a name="performing-the-upgrade"></a>Uitvoeren van de upgrade

### <a name="pre-upgrade"></a>Vóór de upgrade

Als onderdeel van dit, u zou hebben doorlopen de *beoordelen van de gereedheid voor upgrade* sectie en de [plannen voor een upgrade](#planning-for-an-upgrade) sectie van deze handleiding, hebt u alle benodigde informatie ontvangen en die u hebt gemaakt op een abonnement dat u wilt voldoen aan uw behoeften. Waarschijnlijk hebt u een taak testen tijdens deze fase.

### <a name="in-upgrade"></a>In-upgrade

Afhankelijk van de strategie die u kiest en de complexiteit van uw oplossing, deze fase wordt mogelijk een korte naam of een uitgebreide set wanneer er meerdere werkbelastingen die wachten op het incrementeel worden overgezet naar Data Lake Storage Gen2. Dit is de meest kritieke onderdeel van de upgrade.

### <a name="post-upgrade"></a>Na de upgrade

Als u klaar bent met de bewerking overgang, wordt de laatste stappen hebben betrekking op grondige controle. Dit is exclusief, maar niet beperkt tot het verifiëren van gegevens die op betrouw bare wijze zijn gekopieerd, Controleer of de Acl's correct zijn ingesteld, of end-to-end pijp lijnen goed werken, enzovoort. Nadat de verificaties zijn voltooid, kunt u uw oude pijp lijnen nu uitschakelen, uw bron Data Lake Storage Gen1 accounts verwijderen en de volledige snelheid van uw oplossingen op basis van Data Lake Storage Gen2.

## <a name="conclusion"></a>Conclusie

De richtlijnen in dit document dient u een upgrade uitvoeren van uw oplossing voor het gebruik van Data Lake Storage Gen2 hebben bijgedragen. 

Als u meer vragen hebt of feedback hebt, opmerkingen hieronder of geef feedback in de [Forum met Feedback van Azure](https://feedback.azure.com/forums/327234-data-lake).
